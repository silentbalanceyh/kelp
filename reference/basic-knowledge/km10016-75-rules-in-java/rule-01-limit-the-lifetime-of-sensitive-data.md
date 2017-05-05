## Noncompliant Code

```java
class Password{
    public static void main(String args[]) throws IOException{
        Console c = System.console();
        if(c == null){
            System.err.println("No Console.");
            System.exit(1);
        }
        String username = c.readLine("Enter your user name:");
        String password = c.readLine("Enter your password:");
        
        if(!verify(username, password)){
            throw new SecurityException("Invalid Credentials");
        }
        // ...
    }
    
    // Dummy verify method, always returns true
    private static final boolean verify(String username, String password){
        return true;
    }
}
```

## Compliant Code

```java
class Password{
    public static void main(String args[]) throws IOException{
        Console c = System.console();
        if(c == null){
            System.err.println("No Console.");
            System.exit(1);
        }
        String username = c.readLine("Enter your user name:");
        char[] password = c.readPassword("Enter your password:");
        
        if(!verify(username, password)){
            throw new SecurityException("Invalid Credentials");
        }
        // Clear the password
        Arrays.fill(password,'');
    }
    
    // Dummy verify method, always returns true
    private static final boolean verify(String username, char[] password){
        return true;
    }
}
```

password属于敏感数据，使用char\[\]可以在password使用过后直接清除掉，而不保存在Memory中，String属于不可变对象，所以不适合存储密码或敏感数据等信息。

