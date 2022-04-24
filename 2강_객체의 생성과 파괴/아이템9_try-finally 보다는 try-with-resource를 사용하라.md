자바 라이브러리에서는 자원 사용 후 close()로 닫아줘야 하는것들이 있다. 예를들면 InputStream, OutputStream, java.sql.connection 과 같은.

전통적으로 이러한 자원들은 try-finally를 통해 자원을 닫아주었는데, 이 구문은 만약 기계의 물리적인 결함으로 인해 readline()이 실패할 경우 try에서 readLine()에 대한 실패가, finally에서 close()에 대한 실패가 발생한다. 이러한 경우는 시스템상에 close에 대한 예외처리만 잡기 때문에 물리적 결함에 대한 오류 파악이 매우 어렵다는 단점이 있다. 

따라서 자바 7에서부터 제공하는 try-with-resource 구문을 사용하여 자원을 처리하는 것이 좋다. 이 구조를 사용하려면 자원이 AutoCloseable 인터페이스를 구현해야 한다. 이 인터페이스는 close 메소드만 정의한 인터페이스다.

# try-finally 구문

```
public class Copy {
    private static final int BUFFER_SIZE = 8 * 1024;

    // 코드 9-2 자원이 둘 이상이면 try-finally 방식은 너무 지저분하다! (47쪽)
    static void copy(String src, String dst) throws IOException {
        InputStream in = new FileInputStream(src);
        try {
            OutputStream out = new FileOutputStream(dst);
            try {
                byte[] buf = new byte[BUFFER_SIZE];
                int n;
                while ((n = in.read(buf)) >= 0)
                    out.write(buf, 0, n);
            } finally {
                out.close();
            }
        } finally {
            in.close();
        }
    }
}
```

# **하나의 자원을 처리하는 try-with-resources**

```
    // 코드 9-3 try-with-resources - 자원을 회수하는 최선책! (48쪽)
    static String firstLineOfFile(String path) throws IOException {
        try (BufferedReader br = new BufferedReader(
                new FileReader(path))) { // try의 인자값으로 resource 전달
            return br.readLine();
        }
    }
```

# **복수의 자원을 처리하는 try-with-resources**

```
  // 코드 9-4 복수의 자원을 처리하는 try-with-resources - 짧고 매혹적이다! (49쪽)
    static void copy(String src, String dst) throws IOException {
        try (InputStream   in = new FileInputStream(src);
             OutputStream out = new FileOutputStream(dst)) {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0)
                out.write(buf, 0, n);
        }
    }
```

# **try-with-resource with catch**

```
 // 코드 9-5 try-with-resources를 catch 절과 함께 쓰는 모습 (49쪽)
    static String firstLineOfFile(String path, String defaultVal) {
        try (BufferedReader br = new BufferedReader(
                new FileReader(path))) {
            return br.readLine();
        } catch (IOException e) {
            return defaultVal;
        }
    }
```
