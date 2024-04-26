
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
=======
import com.situ.springboot.pojo.User;
import com.situ.springboot.service.IUserService;
import com.situ.springboot.util.PageInfo;
import com.situ.springboot.util.Result;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpSession;
import java.util.List;

@Controller
@RequestMapping("/user")
public class UserController {
    @Autowired
    private IUserService userService;

    @RequestMapping("/selectAll")
    public String selectAll(Model model) {
        try {
            List<User> list = userService.selectAll();
            model.addAttribute("list", list);
        } catch (Exception e) {
            e.printStackTrace();
            // 处理异常，可以返回错误页面或其他提示信息
            model.addAttribute("errorMsg", "查询用户列表失败");
            return "error";
        }
        return "home";
    }

    @RequestMapping("/login")
    @ResponseBody
    public Result login(String name, String password, String code, HttpSession session) {
        try {
            String codeInSession = (String) session.getAttribute("codeInSession");
            if (!codeInSession.equalsIgnoreCase(code)) {
                return Result.error("验证码错误");
            }
            User user = userService.login(name, password);
            if (user != null) {
                if (user.getStatus() == 1) {
                    return Result.error("该用户已被禁用");
                }
                session.setAttribute("user", user);
                return Result.ok("用户登录成功");
            } else {
                return Result.error("用户名或密码错误");
            }
        } catch (Exception e) {
            e.printStackTrace();
            return Result.error("登录异常，请稍后重试");
        }
    }

    @RequestMapping("search")
    public String search(@RequestParam(defaultValue = "1") Integer pageNo,
                         @RequestParam(defaultValue = "100") Integer pageSize, String name, Model model) {
        try {
            if (name != null && !name.isEmpty()) {
                model.addAttribute("name", name);
                List<User> search = userService.searchByName(name);
                PageInfo pageInfo = userService.usernameselectByPage(pageNo, pageSize, name);
                model.addAttribute("pageInfo", pageInfo);
            }
        } catch (Exception e) {
            e.printStackTrace();
            // 处理异常，可以返回错误页面或其他提示信息
            model.addAttribute("errorMsg", "搜索用户失败");
            return "error";
        }
        return "user/user-search";
}