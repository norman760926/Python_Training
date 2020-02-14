# python 與 java 區別

<br>

簡單介紹一下 java 與 python 在編寫上的差異。

<br>
<br>

## 基本格式

* python 的宗旨就是去除沒必要的冗詞贅字，以往大家寫過的 algor like 語言，都習慣句尾加 `;`，區塊包 `{ }`。python 開發者認為這是沒必要的事情，所以 `;` 在 python 中便消失了。而區塊使用的 `{ }` 則用縮排來取代。

    * python

        ```py
        if 2 > 1:
            print("2 > 1")
        else:
            print("1 < 2")
        ```

    * java

        ```java
        if (2 > 1){
            System.out.println("2 > 1");
        }else{
            System.out.println("1 < 2");
        }

    是不是更加明瞭簡潔就見仁見智了。

<br>
<br>

## if 判斷式

* python 的 if 判斷式跟其他任何語言的 if 判斷式差很多，基本上更加的讓 "人" 理解

    * None/null 的判斷 : 

        python
        ```py
        target = None  # java 的 null

        if target is not None:
            print("target 不是 None.")
        
        if target is None:
            print("target 是 None.")
        ```

        java
        ```java
        target = null;
        if (target != null){
            System.out.println("target 不是 null.");
        }

        if (target == null){
            System.out.println("target 是 null.");
        }
        ```

    <br>

    * 用於字串值比對的 equals 方法 : 

        python
        ```py
        target = "Hello"

        if target.__eq__("Hello"):
            print(target)
        ```

        java
        ```java
        target = "Hello";
        if (target.equals("Hello")){
            System.out.println(target);
        }
        ```

    <br>
    <br>

## for 迴圈

* python 的 for 迴圈說實話真的不如 java 的來的靈活，可能真的是為了方便新手 user 使用而做了用法上的一些犧牲。下面直接看範例 : 

    * python 

        ```py
        for i in range(0, 10, 2):  # (起始值，最大值 + 1, step)
            print(i, end=" ")
        ```

    * java
        ```java
        for (int i = 0; i < 10; i=i+2){
            System.out.print(i + " ");
        }
        ```

        console
        ```
        0 2 4 6 8 
        ```

        事實上 python 的 `range(0, 10, 2)` 事在做以下這件事 : 

        ```py
        for i in [0, 2, 4, 6, 8]:  # (起始值，最大值 + 1, step)
            print(i, end=" ")
        ```

        一看就知道這事 forEach 的概念，python 並餔希望我們去處理陣列的 index，但是如果真的有需要用到 index 時，可以這樣做 : 

        ```py
        name_list = ["Johnny", "Ben", "Mikey", "Rod"]
        for index, name in enumerate(name_list):
        print(index, name)
        ```

        ```bash
        0 Johnny
        1 Ben
        2 Mikey
        3 Rod
        ```

        <br>

        因為不能迴圈中取用的 i 並不是索引，而是陣列的值，所以 python 無法做到像 java 的這樣 : 

        當 name 出現 Johnny 時，值接跳過 2 筆資料。
        ```java
        String[] nameList = {"Johnny", "Ben", "Mikey", "Rod"};
        for (int i = 0; i < nameList.length; i++){
            System.out.println(nameList[i]);
            if (nameList[i].equals("Johnny")){
                i = i + 2;
            }
        }
        ```

    <br>
    <br>

## while 迴圈

* python while 迴圈其實與 java 差異不大，需要注意的是，當已知要迴圈幾次時用 `for`，當不知需要迴圈幾次時用 `while`。

    * python
        ```py
        while True:
            print("無窮迴圈 ...")
        ```

    * java
        ```java
        while (true){
            System.out.println("無窮迴圈 ...");
        }
        ```

    <br>
    <br>

## try catch finally

* python 的 try catch finally 結構跟 java 一樣，只是換了一點點關鍵字

    * python
        ```py
        try:
            raise RuntimeError("raise error")
        except Exception as e:
            print(e)
        finally:
            print("end")
        ```

    * java
        ```java
        try {
            throw new RuntimeException("raise error");
        }catch (Exception e){
            System.out.println(e);
        }finally {
            System.out.println("end");
        }
        ```