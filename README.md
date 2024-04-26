import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;

// 用户类
class User {
    private String username;
    private String password;
    private String email;

    public User(String username, String password, String email) {
        this.username = username;
        this.password = password;
        this.email = email;
    }

    public String getUsername() {
        return username;
    }

    public String getPassword() {
        return password;
    }

    public String getEmail() {
        return email;
    }
}

// 用户管理系统
class UserManagementSystem {
    private Map<String, User> users;

    public UserManagementSystem() {
        users = new HashMap<>();
    }

    // 注册新用户
    public void register(String username, String password, String email) {
        if (!users.containsKey(username)) {
            User user = new User(username, password, email);
            users.put(username, user);
            System.out.println("注册成功！");
        } else {
            System.out.println("用户名已存在，请重新选择用户名。");
        }
    }

    // 用户登录
    public User login(String username, String password) {
        if (users.containsKey(username)) {
            User user = users.get(username);
            if (user.getPassword().equals(password)) {
                System.out.println("登录成功！");
                return user;
            } else {
                System.out.println("密码错误，请重新输入。");
            }
        } else {
            System.out.println("用户名不存在，请先注册。");
        }
        return null;
    }
}

public class Main {
    public static void main(String[] args) {
        UserManagementSystem userManagementSystem = new UserManagementSystem();
        Scanner scanner = new Scanner(System.in);

        while (true) {
            System.out.println("欢迎使用新能源汽车移动充电机器人预约系统");
            System.out.println("1. 注册");
            System.out.println("2. 登录");
            System.out.println("3. 退出");

            int choice = scanner.nextInt();
            scanner.nextLine();  // 消耗换行符

            switch (choice) {
                case 1:
                    System.out.println("请输入用户名:");
                    String username = scanner.nextLine();
                    System.out.println("请输入密码:");
                    String password = scanner.nextLine();
                    System.out.println("请输入邮箱:");
                    String email = scanner.nextLine();
                    userManagementSystem.register(username, password, email);
                    break;
                case 2:
                    System.out.println("请输入用户名:");
                    String loginUsername = scanner.nextLine();
                    System.out.println("请输入密码:");
                    String loginPassword = scanner.nextLine();
                    User loggedInUser = userManagementSystem.login(loginUsername, loginPassword);
                    // 在这里可以添加其他功能，比如预约充电服务等
                    break;
                case 3:
                    System.out.println("退出系统。");
                    System.exit(0);
                    break;
                default:
                    System.out.println("无效选项，请重新输入。");
            }
        }
    }
}
