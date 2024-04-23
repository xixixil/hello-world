import java.util.*;

// 用户类
class User {
    String username;
    String password;
    
    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }
}

// 充电机器人类
class ChargingRobot {
    int id;
    boolean available;
    
    public ChargingRobot(int id) {
        this.id = id;
        this.available = true;
    }
    
    public void setAvailability(boolean available) {
        this.available = available;
    }
}

// 充电服务类
class ChargingService {
    ChargingRobot robot;
    User user;
    Date appointmentTime;
    
    public ChargingService(ChargingRobot robot, User user, Date appointmentTime) {
        this.robot = robot;
        this.user = user;
        this.appointmentTime = appointmentTime;
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
            ChargingService service = new ChargingService(availableRobot, currentUser, appointmentTime);
            systemManager.addService(service);
            availableRobot.setAvailability(false);
            System.out.println("充电服务预约成功！");
        } else {
            System.out.println("当前没有可用的充电机器人，请稍后再试！");
        }
    }
}
