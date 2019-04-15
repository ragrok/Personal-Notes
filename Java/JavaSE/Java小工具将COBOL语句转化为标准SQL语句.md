### 需求
项目组接到一个需求，需要将COBOL语句写的东西，和公司的数据字典对比，然后形成一个标准SQL语句，然后和我们项目组的数据表结构来做对比，找出差异。COBOL语句是一种SQL结构语句，一般用在大型的银行系统中。让我们来看下它的样子。
1.COBOL语句
```
******************************************************************        
      * COBOL DECLARATION FOR TABLE CLTGOGA2                          *        
      ******************************************************************        
      01  CLR:GOGA2:.                                                          
          03 :GOGA2:_KEY.                                                      
              05 :GOGA2:_COLL_NO  DIC(GTEE_NO, P).                            
          03 :GOGA2:_R_TYPE      DIC(TYP).                                    
                88 :GOGA2:_R_N          VALUE 'N'.                            
                88 :GOGA2:_R_D          VALUE 'D'.                            
          03 :GOGA2:_GUAR_CI_NO  DIC(CI_NO).                                  
          03 :GOGA2:_GUAR_ENG_NAME DIC(CI_ENM).                                
          03 :GOGA2:_REF_NO      DIC(DESC_30).                                
          03 :GOGA2:_CAN_DATE    DIC(DT, P).                                  
          03 :GOGA2:_GOVER_GUAR_PCT DIC(PCT, P).                              
          03 :GOGA2:_EFF_DATE    DIC(DT, P).                                  
          03 :GOGA2:_EXP_DATE    DIC(DT, P).                                  
          03 :GOGA2:_GUAR_TYP    DIC(PRDMO_CD).                              
          03 :GOGA2:_PREM_TYP    DIC(FLG).                                    
          03 :GOGA2:_CRT_DATE    DIC(DT, P).                                  
          03 :GOGA2:_CRT_TLR      DIC(TLR_NO).                                
          03 :GOGA2:_UPDTBL_DATE  DIC(DT, P).                                  
          03 :GOGA2:_UPDTBL_TLR  DIC(TLR_NO).                                
          03 :GOGA2:_TS          PIC X(26).                                  
      *@*CHECKSUM _2115642531                      
```
2 数据字典，用来对应COBOL语言中的类型(数据字典可能放在EXCEL文档中，有时候需要读取EXCEL文档获取这些数据)
```
ABBR_CD,X,2,0AC,X,25,0
AC-NAME,M,61,0
AC_ARR,X,990,0
AC_EXP,X,2,0
AC_MODEL,X,6,0
```
3 我们想要转化的结果
```
DROP TABLE ODS.CITGID;
CREATE TABLE ODS.CITGID (
GRP_ID CHAR(10) DEFAULT ' ' NOT NULL,
CI_NO CHAR(12) DEFAULT ' ' NOT NULL,
FLG CHAR(1) DEFAULT ' ' NOT NULL,
GRP_NAME CHAR(140) DEFAULT ' ' NOT NULL,
CREATE_UNIT DECIMAL(6,0) DEFAULT 0 NOT NULL,
CREATE_TLR CHAR(8) DEFAULT ' ' NOT NULL,
CREATE_DATE DECIMAL(8,0) DEFAULT 0 NOT NULL,
CREATE_TIME DECIMAL(6,0) DEFAULT 0 NOT NULL,
CHK_STATUS CHAR(1) DEFAULT ' ' NOT NULL,
LAST_UPD_DATE DECIMAL(8,0) DEFAULT 0 NOT NULL,
UPDTBL_DATE DECIMAL(8,0) DEFAULT 0 NOT NULL,
TS CHAR(26) DEFAULT ' ' NOT NULL,
PRIMARY KEY(GRP_ID, CI_NO)
)
IN TBS_REPORT_DATA INDEX IN TBS_REPORT_INDEX COMPRESS YES;
```
### 分析
 语言使用java，里面大概涉及到文件流的处理，怎么读文件，怎么写文件，怎么将文件和数据字典对比，之后将形成的整个文件拆分，形成一个个独立的表文件。我的处理是这样的，首先将COBOL语句转化为一个临时文件，每一行的字符串用切割符分开，之后和数据字典对比，然后形成一个大文件，最后用shell脚本将大文件切割一个个表结构。
 1.   ConverSqlToTxt.java 将COBOL语句转为用 ```!``` 切割的字符串
```
/**
    * @param lineChar 处理03和05开头的字符串
    * @return 符合规范的字符串
    */
    private String checkSqlTabFiled(String lineChar) {
        String str = "";

        String[] splited = lineChar.split("\\s+");
        if (splited[1].contains(":")) {
            splited[1] = splited[1].replace(":", "").trim();
        }

        //去掉第二个数开头的表名
        if (splited[1].contains("_")) {
            String tabName = splited[1];
            //得到tabName截掉前三位的字符串
            tabName = tabName.split("\\_{1}")[0];
            splited[1] = splited[1].replaceFirst(tabName + "_", "").trim();
        }

        if (splited.length == 4) {
            if (splited[2].contains(",") && splited[2].contains("(") && splited[3].contains(")")) {
                splited[2] = splited[2] + splited[3];
                List<String> list = new ArrayList<String>();
                for (int i = 0; i < splited.length; i++) {
                    list.add(splited[i]);
                }
                //去掉第四位的数组
                list.remove(3);
                splited = list.toArray(new String[1]);
            }
        }


        if (splited.length == 3) {
            str = splited[0] + "!" + splited[1] + "!" + splited[2];
        } else if (splited.length == 4) {
            str = splited[0] + "!" + splited[1] + "!" + splited[2] + "!" + splited[3];
        } else if (splited.length == 6 && splited[1].equals(sevenNumber)) {
            str = splited[0] + "!" + splited[1]  + "!" + splited[3] + "!" + splited[4] + "!" + splited[5];
        }
        return str.trim();
    }


    /**
    * @param lineChar 行数
    * @return 符合规范的字符
    */
    private String checkSqlTabName(String lineChar, List<String> keys, int lineInt) {
        String str = "";
        StringBuilder builder = new StringBuilder();

        str = lineChar.replaceFirst("R:", "T:").trim();
        str = str.replace(":", "").trim();
        String[] splited = str.split("\\s+");
        str = splited[0] + "!" + splited[1];
        if (keys.isEmpty() && lineInt > 1) {
            builder.append("06!KEY \n \n");
        } else if (!keys.isEmpty() && lineInt > 1) {
            //倒序
            //Collections.reverse(keys);
            String keyStr = keys.toString();
            System.out.println("keyStr:"+keyStr);
            keyStr = keyStr.replaceFirst("\\[", "").trim();
            keyStr = keyStr.replaceFirst("\\]", "").trim();
            builder.append("06!PRIMARY KEY(" + keyStr + ") \n \n");
            keys.clear();
        }
        builder.append(str);
        return builder.toString();
    }
```
1.得到的结果
```
01!BPCCHMAK
03!SEQ!DIC(JRN_SEQ)03!JRNNO!DIC(JRNNO)
03!JRN_SEQ!DIC(JRN_SEQ)03!TX_TM!DIC(TM)
03!TX_CD!DIC(TX_CD)
03!TX_TYPE!DIC(FRE_CD10)
03!MAKER!DIC(TLR_NO)
03!CHECKER!DIC(TLR_NO)
03!FIELD_NAME!DIC(X20)
03!OLD_VALUE!DIC(DESC_50)
03!NEW_VALUE!DIC(DESC_50)06!KEY 
```
2. CopyBookToSql.java  将临时文件和数据字典对比转化为SQL语言
```
/**   
   * @param arry1
    * @param arry2    存放表中字段的数据，以03和05开头，长度在3和4之间
    * @param lineChar 存放数据字典的对应表类型的数据，DIC需要参考数据字典，PIC不需要参考数据字典，直接拿就行
    * @return
    */
    private String splitSql(String[] arry1, String[] arry2, String lineChar) {

        if (arry1[0].equals(treeNumber) || arry1[0].equals(fourNumber) || arry1[0].equals(fiveNumber)) {
            if (arry1[2].equals(PIC)) {
                // 以X开头的
                if (arry1[3].startsWith(PIC_X)) {
                    String number = getNumbers(arry1[3]);
                    if (Integer.valueOf(number) != 26) {
                        lineChar = arry1[1] + "\t" + "CHAR(" + number + ")" + "\t" + "DEFAULT ' ' NOT NULL ,";
                    } else {
                        lineChar = arry1[1] + "\t" + "TIMESTAMP DEFAULT CURRENT_TIMESTAMP ,";
                    }
                } else if (arry1[3].startsWith(PIC_S)) {
                    String number = getSNine(arry1[3]);
                    lineChar = arry1[1] + "\t" + "DECIMAL(" + number + ",0)" + "\t" + "DEFAULT 0 NOT NULL,";
                } else if (arry1[3].startsWith(PIC_N)) {
                    int number = Integer.valueOf(getNumbers(arry1[3]));
                    lineChar = arry1[1] + "\t" + "DECIMAL(" + number + ",0)" + "\t" + "DEFAULT 0 NOT NULL ,";
                }

            } else if (arry1[2].startsWith(DIC)) {
                // 第三个数中不带有逗号
                if (arry1[2].contains(",")) {
                    String str = getTwoBranket(arry1[2])[0];
                    // 匹配到某个关键字
                    if (arry2[0].equals(str)) {
                        // X类型
                        if (PIC_X.equals(arry2[1])) {
                            lineChar = arry1[1] + "\t" + "CHAR(" + arry2[2] + ")" + "\t" + "DEFAULT ' ' NOT NULL,";
                        }
                        // N类型
                        if (PIC_N.equals(arry2[1])) {
                            lineChar = arry1[1] + "\t" + "DECIMAL(" + arry2[2] + "," + arry2[3] + ")" + "\t"
                                    + "DEFAULT 0 NOT NULL,";
                        }
                        // M类型
                        if (PIC_M.equals(arry2[1])) {
                            int num = Integer.valueOf(arry2[2]) * 3;
                            if (num >= 255) {
                                lineChar = arry1[1] + "\t" + "VARCHAR(" + num + ")" + "\t" + "DEFAULT ' ' NOT NULL,";
                            } else {
                                lineChar = arry1[1] + "\t" + "CHAR(" + num + ")" + "\t" + "DEFAULT ' ' NOT NULL,";
                            }
                        }
                        // V类型
                        if (PIC_V.equals(arry2[1])) {
                            int num = Integer.valueOf(arry2[2]);
                            System.out.println("警告！！！！！这是" + PIC_V + "的情况");
                            if (num >= 255) {
                                lineChar = arry1[1] + "\t" + "VARCHAR(" + num + ")" + "\t" + "DEFAULT ' ' NOT NULL,";
                            } else {
                                lineChar = arry1[1] + "\t" + "CHAR(" + num + ")" + "\t" + "DEFAULT ' ' NOT NULL,";
                            }
                        }
                    }
                    // 第三个数带有逗号
                } else if (!arry1[2].contains(",")) {
                    String str = getOneBranket(arry1[2]);
                    // 匹配到某个关键字
                    if (arry2[0].equals(str)) {
                        // X类型
                        if (PIC_X.equals(arry2[1])) {
                            lineChar = arry1[1] + "\t" + "CHAR(" + arry2[2] + ")" + "\t" + "DEFAULT ' ' NOT NULL,";
                        }
                        // N类型
                        if (PIC_N.equals(arry2[1])) {
                            lineChar = arry1[1] + "\t" + "DECIMAL(" + arry2[2] + "," + arry2[3] + ")" + "\t"
                                    + "DEFAULT 0 NOT NULL,";
                        }
                        // M类型
                        if (PIC_M.equals(arry2[1])) {
                            int num = Integer.valueOf(arry2[2]) * 3;
                            if (num >= 255) {
                                lineChar = arry1[1] + "\t" + "VARCHAR(" + num + ")" + "\t" + "DEFAULT ' ' NOT NULL,";
                            } else {
                                lineChar = arry1[1] + "\t" + "CHAR(" + num + ")" + "\t" + "DEFAULT ' ' NOT NULL,";
                            }
                        }
                        // V类型
                        if (PIC_V.equals(arry2[1])) {
                            int num = Integer.valueOf(arry2[2]);
                            //System.out.println("警告！！！！！这是" + PIC_V + "的情况");
                            if (num >= 255) {
                                lineChar = arry1[1] + "\t" + "VARCHAR(" + num + ")" + "\t" + "DEFAULT ' ' NOT NULL,";
                            } else {
                                lineChar = arry1[1] + "\t" + "CHAR(" + num + ")" + "\t" + "DEFAULT ' ' NOT NULL,";
                            }
                        }
                    }
                }

            }
        } else if (arry1[0].equals(sevenNumber)) {
            String filedName = arry1[1];
            int fileNumber = Integer.valueOf(arry1[2]);
            StringBuilder builder = new StringBuilder();

            if ((arry1[3]).startsWith(PIC)) {
                String str = getNine(arry1[4]);
                lineChar = "DECIMAL(" + str + ",0)" + "\t" + "DEFAULT 0 NOT NULL,";
            }

            for (int i = 1; i < fileNumber; i++) {
                builder.append(filedName + i + "\t \t" + lineChar + "\n");
            }
            lineChar = builder.toString();

        } else {
          // System.out.println("这些都是异常情况");
        }
        return lineChar;
    }
```
3  main 方法
```
public class CopyBookMain {
            public static void main(String[] args) throws IOException {
            ConverSqlToTxt sqlToTxt = new ConverSqlToTxt();
            sqlToTxt.readBookFileByLine("E:\\git\\Practice_JAVA\\IBSC_BOOK_1.SQL","E:\\git\\Practice_JAVA\\IBSC_BOOK_1.txt");  
           CopyBookToSql sql = new CopyBookToSql();
            sql.copyBookTosql("E:\\git\\Practice_JAVA\\IBSC_BOOK_1.txt","E:\\git\\Practice_JAVA\\dict.dat"
                    //,"H:\\github\\Practice_JAVA\\SQL\\","ODS");
                    ,"E:\\git\\Practice_JAVA\\IBSC_BOOK_2.sql","ODS");
        }
}

```
4   脚本语言 切割文本，形成一个个单独的表结构
```
#这个脚本用来将java程序转化的sql程序拆开为独立的表结构
filePath=$1
sqlDerectory=$2
tabStart='DROP'

clearFile(){
    rm -f $1/*
}

clearNullLine(){
  cat $1 | sed -e '/^$/d' > $1
}
readeCopyBook(){
    echo $1 $2 $3
    tabname=''
    cat $1 | while read line
    do
      #处理tabname
      if [[ ${line} != '' ]]; then
        result=`echo ${line} | grep $3`
        if [[ ${result} != '' ]]; then
          tabname=${result#*.}
          tabname=${tabname%;*}
        fi
      fi
      #处理sql文件
      if [[ ${line} != '' ]]; then
        echo ${line} >> $2/${tabname}".sql"
      fi
    done
}


main(){
  #清理上次是否残存文件
  clearFile ${sqlDerectory}
  #清理空行
  clearNullLine ${filePath}
  #运行reade函数  copybook文件名  拆分表结构的文件夹
  readeCopyBook ${filePath} ${sqlDerectory} ${tabStart}

}

main
```
###  结尾
如果想要完整的代码，请访问我的github：[java copybook 小工具转化COBOL语句](https://github.com/ragrok/CopyBook_JAVA)。