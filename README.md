#   Lambda 表达式

##  GitHub :  
*   让我们从最简单的例子开始，来学习如何对一个string列表进行排序：

```java
package top.bfylu;

import org.junit.Test;

import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

/**
 * Lambda使用例子
 * @author bfy--lujian
 * @version 1.0.0
 * 创建时间：2018/8/16 10:29
 * @email bfyjian@gmail.com
 */
public class LambdaTest {

    /**************** 对一个string列表进行排序 start *******************/
    
    
    //我们首先使用Java 8之前的方法来实现：
    @Test
    public void test1() {
        //Arrays.asList不支持add和remove方法
        List<String> names = Arrays.asList("peter", "anna", "mike", "xenia");
        //对一个string列表进行排序
        Collections.sort(names, new Comparator<String>() {
            public int compare(String a, String b) {
                return b.compareTo(a);
            }
        });
        for (String name : names) {
            System.out.println("before names:" + name);
        }

    }

    //使用Lambad表达式
    @Test
    public void test2() {
        List<String> names = Arrays.asList("peter", "anna", "mike", "xenia", "bfylu");
        //Lambad表达式
        Collections.sort(names, (String a,String  b) ->{
            return b.compareTo(a);
        });
        for (String name : names) {
            System.out.println("before names:" + name);
        }
    }
    //你可以看到，这段代码就比之前的更加简短和易读。但是，它还可以更加简短：
    @Test
    public void test3() {
        List<String> names = Arrays.asList("peter", "anna", "mike", "xenia", "bfylu");
        //lambad
        Collections.sort(names, (String a,String b) -> (b.compareTo(a)));

        for (String name : names) {
            System.out.println("before names:" + name);
        }
    }

    //只要一行代码，包含了方法体。你甚至可以连大括号对{}和return关键字都省略不 要。不过这还不是最短的写法：
    @Test
    public void test4() {
        List<String> names = Arrays.asList("peter", "anna", "mike", "xenia", "bfylu");
        //lambad
        Collections.sort(names, (a, b) -> (b.compareTo(a)));

        for (String name : names) {
            System.out.println("before names:" + name);
        }
    }
    //Java编译器能够自动识别参数的类型，所以你就可以省略掉类型不写
    /**************** 对一个string列表进行排序 end *******************/
    
}

```

### 函数式接口

*   Lambda表达式如何匹配Java的类型系统？每一个lambda都能够通过一个特定的接 口，与一个给定的类型进行匹配。一个所谓的函数式接口必须要
    有且仅有一个抽象 方法声明。每个与之对应的lambda表达式必须要与抽象方法的声明相匹配。由于默 认方法不是抽象的，因此你可以在你的函数
    式接口里任意添加默认方法。 
    
    任意只包含一个抽象方法的接口，我们都可以用来做成lambda表达式。为了让你定 义的接口满足要求，你应当在接口前加上@FunctionalInterface
    标注。编译器会注 意到这个标注，如果你的接口中定义了第二个抽象方法的话，编译器会抛出异常。
    
    举例：
``` 
    // 一个所谓的函数式接口必须要有且仅有一个抽象 方法声明
    @FunctionalInterface
    interface Converter<F, T> {
        T convert(F from);
    }

    //使用Java 8之前的方法来实现：
    @Test
    public void test5() {
        Converter<String, Integer> converter = new Converter<String, Integer>() {
            @Override
            public Integer convert(String from) {
                return Integer.valueOf(from);
            }
        };

        Integer converted = converter.convert("123");
        System.out.println(converted);
    }
    
    @Test
    public void test6() {

        Converter<String, Integer> converter = (from) -> Integer.valueOf(from);

        Integer converted = converter.convert("123");
        System.out.println(converted);  //123
    }

```    
*   注意，如果你不写@FunctionalInterface	标注，程序也是正确的。


####方法和构造函数引用

*   上面的代码实例可以通过静态方法引用，使之更加简洁：

``` 
@Test
    public void test7() {
        Converter<String, Integer> converter = Integer::valueOf;
        Integer converted = converter.convert("123");
        System.out.println(converted);  //"123"
    }
```    

*   Java 8 允许你通过::关键字获取方法或者构造函数的的引用。上面的例子就演示了 如何引用一个静态方法。
    而且，我们还可以对一个对象的方法进行引用：

``` 
    class Something {
        String startsWith(String s) {
            return String.valueOf(s.charAt(0));
        }
    }
    @Test
    public void test8() {
        Something something = new Something();
        Converter<String, String> converter = something::startsWith;
        String converted = converter.convert("Java");
        System.out.println(converted);  //"J"
    }
    
```    
    
*   让我们看看如何使用::关键字引用构造函数。首先我们定义一个示例bean，包含不 同的构造方法：

``` 
    class Person {
        String firstName;
        String lastName;

        Person() {}

        Person(String firstName, String lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }
    }
```   

*    接下来，我们定义一个person工厂接口，用来创建新的person对象：

``` 
    interface PersonFactory<P extends Person> {
        P create(String firstName, String lastName);
    }
```

*   java8之前
``` 
    @Test
    public void test9() {
        PersonFactory<Person> personPersonFactory = new PersonFactory<Person>() {
            @Override
            public Person create(String firstName, String lastName) {
                return new Person(firstName, lastName);
            }
        };
        Person person = personPersonFactory.create("Peter", "parker");
        System.out.println(person.firstName);

    }
```    
*   然后我们通过构造函数引用来把所有东西拼到一起，而不是像以前一样，通过手动 实现一个工厂来这么做。       
    
```
 @Test
    public void test10() {
        PersonFactory<Person> personPersonFactory = Person::new;
        Person person = personPersonFactory.create("Peter", "Parker");
        System.out.println(person.lastName);
    }
```    
*   我们通过Person::new来创建一个Person类构造函数的引用。Java编译器会自动地
    选择合适的构造函数来匹配PersonFactory.create函数的签名，并选择正确的构造 函数形式。


### Lambda的范围 

*   对于lambdab表达式外部的变量，其访问权限的粒度与匿名对象的方式非常类似。 
    你能够访问局部对应的外部区域的局部final变量，以及成员变量和静态变量。
    
####访问局部变量 

*   我们可以访问lambda表达式外部的final局部变量：

``` 
final int	num	= 1; 
Converter<Integer, String>	stringConverter	= (from) ->	String.valueOf(from	+ num);
stringConverter.convert(2);					//	3
```   

*   但是与匿名对象不同的是，变量num并不需要一定是final。下面的代码依然是合法 的：
``` 
int	num	= 1; Converter<Integer, String>	stringConverter	= (from) -> String.valueOf(from	+ num);
stringConverter.convert(2);     //	3
```

*   然而，num在编译的时候被隐式地当做final变量来处理。下面的代码就不合法：

```

int	num	= 1; Converter<Integer, String> stringConverter	= (from) ->	String.valueOf(from	+ num); num	= 3;

```   
*   在lambda表达式内部企图改变num的值也是不允许的。

####访问成员变量和静态变

*   与局部变量不同，我们在lambda表达式的内部能获取到对成员变量或静态变量的读写权。
    这种访问行为在匿名对象里是非常典型的。
    
``` 
    public class Lambda4 {
        static int outerStaticNum;
        int outerNum;
        void testScopes() {
            Converter<Integer, String> stringConverter = from -> {
                outerNum = 23;
                return String.valueOf(from);
            };
            Converter<Integer, String> stringConverter2 = from -> {
                outerStaticNum = 72;
                return String.valueOf(from);
            };
        }
    }
```    

###LambdaTest.java

```java

package top.bfylu;

import org.junit.Test;
import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

/**
 * Lambda使用例子
 * @author bfy--lujian
 * @version 1.0.0
 * 创建时间：2018/8/16 10:29
 * @email bfyjian@gmail.com
 */
public class LambdaTest {

    /**************** 对一个string列表进行排序 start *******************/

    @Test
    public void test1() {
        //Arrays.asList不支持add和remove方法
        List<String> names = Arrays.asList("peter", "anna", "mike", "xenia");
        //对一个string列表进行排序
        Collections.sort(names, new Comparator<String>() {
            public int compare(String a, String b) {
                return b.compareTo(a);
            }
        });
        for (String name : names) {
            System.out.println("before names:" + name);
        }

    }

    //使用Lambad表达式
    @Test
    public void test2() {
        List<String> names = Arrays.asList("peter", "anna", "mike", "xenia", "bfylu");
        //Lambad表达式
        Collections.sort(names, (String a,String  b) ->{
            return b.compareTo(a);
        });
        for (String name : names) {
            System.out.println("before names:" + name);
        }
    }
    //你可以看到，这段代码就比之前的更加简短和易读。但是，它还可以更加简短：
    @Test
    public void test3() {
        List<String> names = Arrays.asList("peter", "anna", "mike", "xenia", "bfylu");
        //lambad
        Collections.sort(names, (String a,String b) -> (b.compareTo(a)));

        for (String name : names) {
            System.out.println("before names:" + name);
        }
    }

    //只要一行代码，包含了方法体。你甚至可以连大括号对{}和return关键字都省略不 要。不过这还不是最短的写法：
    @Test
    public void test4() {
        List<String> names = Arrays.asList("peter", "anna", "mike", "xenia", "bfylu");
        //lambad
        Collections.sort(names, (a, b) -> (b.compareTo(a)));

        for (String name : names) {
            System.out.println("before names:" + name);
        }
    }
    //Java编译器能够自动识别参数的类型，所以你就可以省略掉类型不写
    /**************** 对一个string列表进行排序 end *******************/

    /********************* 函数式接口 start**************************/

    // 一个所谓的函数式接口必须要有且仅有一个抽象 方法声明
    @FunctionalInterface
    interface Converter<F, T> {
        T convert(F from);
    }

    //使用Java 8之前的方法来实现：
    @Test
    public void test5() {
        Converter<String, Integer> converter = new Converter<String, Integer>() {
            @Override
            public Integer convert(String from) {
                return Integer.valueOf(from);
            }
        };

        Integer converted = converter.convert("123");
        System.out.println(converted);
    }


    @Test
    public void test6() {

        Converter<String, Integer> converter = (from) -> Integer.valueOf(from);

        Integer converted = converter.convert("123");
        System.out.println(converted);  //123
    }

    /**
     * 方法和构造函数引用
     * 上面的代码实例可以通过静态方法引用，使之更加简洁
     */
    @Test
    public void test7() {
        Converter<String, Integer> converter = Integer::valueOf;
        Integer converted = converter.convert("123");
        System.out.println(converted);  //"123"
    }

    /**Java	8	允许你通过::关键字获取方法或者构造函数的的引用。上面的例子就演示了 如何引用一个静态方法。
     * 而且，我们还可以对一个对象的方法进行引用：
     */
    class Something {
        String startsWith(String s) {
            return String.valueOf(s.charAt(0));
        }
    }
    @Test
    public void test8() {
        Something something = new Something();
        Converter<String, String> converter = something::startsWith;
        String converted = converter.convert("Java");
        System.out.println(converted);  //"J"
    }

    /**
     * 让我们看看如何使用::关键字引用构造函数。首先我们定义一个示例bean，包含不 同的构造方法：
     */
    class Person {
        String firstName;
        String lastName;

        Person() {}

        Person(String firstName, String lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }
    }

    /**
     * 接下来，我们定义一个person工厂接口，用来创建新的person对象：
     * @param <P>
     */
    interface PersonFactory<P extends Person> {
        P create(String firstName, String lastName);
    }

    /**
     * java8之前
     */
    @Test
    public void test9() {
        PersonFactory<Person> personPersonFactory = new PersonFactory<Person>() {
            @Override
            public Person create(String firstName, String lastName) {
                return new Person(firstName, lastName);
            }
        };
        Person person = personPersonFactory.create("Peter", "parker");
        System.out.println(person.firstName);

    }

    /**
     * 然后我们通过构造函数引用来把所有东西拼到一起，而不是像以前一样，通过手动 实现一个工厂来这么做。
     */
    @Test
    public void test10() {
        PersonFactory<Person> personPersonFactory = Person::new;
        Person person = personPersonFactory.create("Peter", "Parker");
        System.out.println(person.lastName);
    }
    //我们通过Person::new来创建一个Person类构造函数的引用。Java编译器会自动地
    //选择合适的构造函数来匹配PersonFactory.create函数的签名，并选择正确的构造 函数形式。

    /********************* 函数式接口 end **************************/

    /*********************** Lambda的范围 start ************************/

    /**
     * 对于lambdab表达式外部的变量，其访问权限的粒度与匿名对象的方式非常类似。
     * 你能够访问局部对应的外部区域的局部final变量，以及成员变量和静态变量。
     */

    /**
     * 访问局部变量
     * 我们可以访问lambda表达式外部的final局部变量：
     */
    //java8之前
    @Test
    public void test11() {
        final int num = 1;
        Converter<Integer, String> stringConverter = new Converter<Integer, String>() {
            @Override
            public String convert(Integer from) {
                return String.valueOf(from + num);
            }
        };
        stringConverter.convert(2); //3
        System.out.println(stringConverter.convert(2));
    }

    @Test
    public void test12() {
        int num = 1;
        Converter<Integer, String> stringConverter = from -> String.valueOf(from + num);
        stringConverter.convert(2); // 3
        System.out.println(stringConverter.convert(2));
    }

    /**
     * 访问成员变量和静态变量
     */
    //与局部变量不同，我们在lambda表达式的内部能获取到对成员变量或静态变量的读 写权。
    //这种访问行为在匿名对象里是非常典型的
    static int outerStaticNum;
    class Lambda4 {

        int outerNum;
        void testScopes() {
            Converter<Integer, String> stringConverter = from -> {
                outerNum = 23;
                return String.valueOf(from);
            };
            Converter<Integer, String> stringConverter2 = from -> {
                outerStaticNum = 72;
                return String.valueOf(from);
            };
        }
    }
    /*********************** Lambda的范围 end ************************/
}

```




    


    
    



