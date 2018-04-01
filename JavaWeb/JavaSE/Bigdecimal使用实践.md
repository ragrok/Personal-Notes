－最近开发用到bigdecimal比较多，看了几篇博文，实践下，知其所以然。
#### 参考博文
- [BigDecimal 使用详解](http://zhangyinhu8680.iteye.com/blog/1536397)
- [详细BigDecimal 算法请参考](http://zhenchengchagangzi.iteye.com/blog/1258453)
- [java BigDecimal 使用详解](http://blog.csdn.net/jackiehff/article/details/8582449)
```
package cn.com.github2;
import java.math.BigDecimal;
public class TestBigdecimal {
    // 初始化
    public void bigdecimalConstruct() {
        BigDecimal bigDecimal = BigDecimal.valueOf(1.22);
        System.out.println("value of bigDecimal:" + bigDecimal);
        // 使用double数值初始化
        BigDecimal dDouble = new BigDecimal(1.22);
        System.out.println("construct double value:" + dDouble);
        // 使用string数值初始化
        BigDecimal bString = new BigDecimal("1.22");
        System.out.println("construct string value:" + bString);
        // 使用String的valueof方法来初始化数值
        BigDecimal dToString = new BigDecimal(String.valueOf(1.22));
        System.out.println("construct dToString value:" + dToString);
        // 使用double的tostring方法来初始化数值
        BigDecimal dToDouble = new BigDecimal(Double.toString(1.22));
        System.out.println("construct dToDouble value:" + dToDouble);
        // output
        /*
         * value of bigDecimal:1.22 
         * construct double value:1.2199999999999999733546474089962430298328399658203125
         * construct string value:1.22 
         * construct dToString value:1.22 
         * construct dToDouble value:1.22
         */
    }
    // 基本运算，以加为例
    public void calculateBigdecimal() {
        BigDecimal addBigdecimal = new BigDecimal(1.22);
        // bigdecimal做这些基本运算，基本就是会返回一个新的对象，如果没有新的对象接收，返回的还是原来的值
        addBigdecimal.add(BigDecimal.valueOf(1.33));
        System.out.println("add calculate1:" + addBigdecimal);
        // add calculate1:1.2199999999999999733546474089962430298328399658203125
        // 有新对象接收，才返回计算的值
        addBigdecimal = addBigdecimal.add(BigDecimal.valueOf(1.33));
        System.out.println("add calculate2:" + addBigdecimal);
        // add calculate2:2.5499999999999999733546474089962430298328399658203125
    }
    // 精度运算,格式化小数点，以经典的四舍五入为例
    public void scaleBigdecimal() {
        BigDecimal addBigdecimal = new BigDecimal(1.2245);
        BigDecimal addBigdecimal1 = new BigDecimal(1.2244);
        BigDecimal addBigdecimal2 = new BigDecimal(1.2246);
        // 四舍五入
        addBigdecimal = addBigdecimal.setScale(3, BigDecimal.ROUND_HALF_UP);
        //计算小数点后有多少位
        System.out.println("scale value:" + addBigdecimal.scale());
        //scale value:3
        System.out.println("ROUND_HALF_UP value:" + addBigdecimal);
        // ROUND_HALF_UP value:1.224，这里有很大的疑问，为何不进位？
        addBigdecimal1 = addBigdecimal1.setScale(3, BigDecimal.ROUND_HALF_UP);
        System.out.println("ROUND_HALF_UP value:" + addBigdecimal1);
        // ROUND_HALF_UP value:1.224
        addBigdecimal2 = addBigdecimal2.setScale(3, BigDecimal.ROUND_HALF_UP);
        System.out.println("ROUND_HALF_UP value:" + addBigdecimal2);
        // ROUND_HALF_UP value:1.225
        BigDecimal addBigdecima3 = new BigDecimal(1.2255);
        BigDecimal addBigdecimal4 = new BigDecimal(1.2254);
        BigDecimal addBigdecimal5 = new BigDecimal(1.2256);
        // 四舍五入
        addBigdecima3 = addBigdecima3.setScale(3, BigDecimal.ROUND_HALF_UP);
        System.out.println("ROUND_HALF_UP value:" + addBigdecima3);
        // ROUND_HALF_UP value:1.226
        addBigdecimal4 = addBigdecimal4.setScale(3, BigDecimal.ROUND_HALF_UP);
        System.out.println("ROUND_HALF_UP value:" + addBigdecimal4);
        // ROUND_HALF_UP value:1.225
        addBigdecimal5 = addBigdecimal5.setScale(3, BigDecimal.ROUND_HALF_UP);
        System.out.println("ROUND_HALF_UP value:" + addBigdecimal5);
        // ROUND_HALF_UP value:1.226
    }
        //比较运算
    public void compareToBigdecimal(){
        //使用string来初始化
        BigDecimal compareValue1 = new BigDecimal("1.00");
        //使用double来初始化
        BigDecimal compareValue2 = new BigDecimal(1);
        //使用equals，equals会比较类型和值
        System.out.println("equals result:"+compareValue1.equals(compareValue2));
        //equals result:false
        //使用compareto，只比较值
        System.out.println("equals result:"+(compareValue1.compareTo(compareValue2) == 0));
        //compareTo result:true
    }
    
    public static void main(String[] args) {
        TestBigdecimal testBigdecimal = new TestBigdecimal();
        testBigdecimal.bigdecimalConstruct();
        testBigdecimal.calculateBigdecimal();
        testBigdecimal.scaleBigdecimal();
        testBigdecimal.compareToBigdecimal();
    }
}
```