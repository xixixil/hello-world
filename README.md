import java.util.*;

// 用户类
class User {
    private String username;
    private String password;
    
    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }
    
    // Getter 方法
    public String getUsername() {
        return username;
    }
}

// 充电机器人类
class ChargingRobot {
    private int id;
    private boolean available;
    
    public ChargingRobot(int id) {
        this.id = id;
        this.available = true;
    }
    
    // Getter 和 Setter 方法
    public int getId() {
        return id;
    }
    
    public boolean isAvailable() {
        return available;
    }
    
    public void setAvailable(boolean available) {
        this.available = available;
    }
}

// 充电服务类
class ChargingService {
    private ChargingRobot robot;
    private User user;
    private Date appointmentTime;
    
    public ChargingService(ChargingRobot robot, User user, Date appointmentTime) {
        this.robot = robot;
        this.user = user;
        this.appointmentTime = appointmentTime;
    }
    
    // Getter 方法
    public ChargingRobot getRobot() {
        return robot;
    }
}

// 系统管理类
class SystemManager {
    private List<User> users;
    private List<ChargingRobot> robots;
    private List<ChargingService> services;
    
    public SystemManager() {
        this.users = new ArrayList<>();
        this.robots = new ArrayList<>();
        this.services = new ArrayList<>();
    }
    
    // 添加用户
    public void addUser(User user) {
        users.add(user);
    }
    
    // 添加充电机器人
    public void addRobot(ChargingRobot robot) {
        robots.add(robot);
    }
    
    // 预约充电服务
    public void bookChargingService(User user, ChargingRobot robot, Date appointmentTime) {
        ChargingService service = new ChargingService(robot, user, appointmentTime);
        services.add(service);
        robot.setAvailable(false);
        System.out.println("用户 " + user.getUsername() + " 预约了充电服务，机器人编号：" + robot.getId());
    }
    
    // 查找可用机器人
    public ChargingRobot findAvailableRobot() {
        for (ChargingRobot robot : robots) {
            if (robot.isAvailable()) {
                return robot;
            }
        }
        return null;
    }
}

public class Main {
    public static void main(String[] args) {
        SystemManager systemManager = new SystemManager();
        
        // 添加用户和机器人到系统
        User user1 = new User("user1", "password1");
        User user2 = new User("user2", "password2");
        systemManager.addUser(user1);
        systemManager.addUser(user2);
        
        ChargingRobot robot1 = new ChargingRobot(1);
        ChargingRobot robot2 = new ChargingRobot(2);
        systemManager.addRobot(robot1);
        systemManager.addRobot(robot2);
        
        // 用户登录
        User currentUser = user1; // 假设当前用户是user1
        
        // 预约充电服务
        ChargingRobot availableRobot = systemManager.findAvailableRobot();
        if (availableRobot != null) {
            Date appointmentTime = new Date(); // 假设预约时间为当前时间
            systemManager.bookChargingService(currentUser, availableRobot, appointmentTime);
        } else {
            System.out.println("当前没有可用的充电机器人，请稍后再试！");
        }
    }
}
