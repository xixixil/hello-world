
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

// 充电机器人类
class ChargingRobot {
    private String type;
    private String location;
    private boolean available;

    public ChargingRobot(String type, String location) {
        this.type = type;
        this.location = location;
        this.available = true;
    }

    public String getType() {
        return type;
    }

    public String getLocation() {
        return location;
    }

    public boolean isAvailable() {
        return available;
    }

=======

package com.example.demo.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.imageio.ImageIO;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.IOException;
import java.util.Random;

@Controller
@RequestMapping("/auth")
public class AuthCodeController {
    private char[] codeSequence = { 'A', '1','B', 'C', '2','D','3', 'E','4', 'F', '5','G','6', 'H', '7','I', '8','J',
            'K',   '9' ,'L', '1','M',  '2','N',  'P', '3', 'Q', '4', 'R', 'S', 'T', 'U', 'V', 'W',
            'X', 'Y', 'Z'};

    // /auth/code
    @RequestMapping("/code")
    public void getCode(HttpServletResponse response, HttpSession session) throws IOException {
        int width = 63;
        int height = 37;
        Random random = new Random();
        //设置response头信息
        //禁止缓存
        response.setHeader("Pragma", "No-cache");
        response.setHeader("Cache-Control", "no-cache");
        response.setDateHeader("Expires", 0);

        //生成缓冲区image类
        BufferedImage image = new BufferedImage(width, height, 1);
        //产生image类的Graphics用于绘制操作
        Graphics g = image.getGraphics();
        //Graphics类的样式
        g.setColor(this.getColor(200, 250));
        g.setFont(new Font("Arial",0,28));
        g.fillRect(0, 0, width, height);
        //绘制干扰线
        for(int i=0;i<40;i++){
            g.setColor(this.getColor(130, 200));
            int x = random.nextInt(width);
            int y = random.nextInt(height);
            int x1 = random.nextInt(12);
            int y1 = random.nextInt(12);
            g.drawLine(x, y, x + x1, y + y1);
        }

        //绘制字符
        String strCode = "";
        for(int i=0;i<4;i++){
            String rand = String.valueOf(codeSequence[random.nextInt(codeSequence.length)]);
            strCode = strCode + rand;
            g.setColor(new Color(20+random.nextInt(110),20+random.nextInt(110),20+random.nextInt(110)));
            g.drawString(rand, 13*i+6, 28);
        }
        //将字符保存到session中用于前端的验证
        session.setAttribute("codeInSession", strCode.toLowerCase());
        g.dispose();

        ImageIO.write(image, "JPEG", response.getOutputStream());
        response.getOutputStream().flush();
    }

    public  Color getColor(int fc,int bc){
        Random random = new Random();
        if(fc>255)
            fc = 255;
        if(bc>255)
            bc = 255;
        int r = fc + random.nextInt(bc - fc);
        int g = fc + random.nextInt(bc - fc);
        int b = fc + random.nextInt(bc - fc);
        return new Color(r,g,b);
    }
}

@Controller
@RequestMapping("/customer")
public class CustomerController {
    @Autowired
    private ICustomerService customerService;

    @RequestMapping("/register")
    public String register() {
        return "customer_register";
    }

    @RequestMapping("/register_success")
    public String register_success(Customer customer) {
        customerService.add(customer);
        return "customer_register_success";
    }


    @RequestMapping("/toLogin")
    public String toLogin() {
        return "customer_login";
    }


    @RequestMapping("/login")
    public String login(String name, String password, HttpSession session){
        //根据name和pw查这个用户有没有
        //登录成功跳转
        //登录凭证放到session
        Customer customer=customerService.login(name,password);
        if(customer!=null&&customer.getRole()==1){
            session.setAttribute("customer",customer);
            return "redirect:/customer/tointer"; //登录成功跳转
        }
        else {
            return "redirect:/customer/toLogin"; //登录失败重定向
        }
    }

    @RequestMapping("/login1")
    public String login1(){
        return "redirect:/customer/tointer";
    }

    @RequestMapping("/logout")
    public String logout(HttpSession session){
        session.invalidate();
        return "redirect:/customer/toLogin";
    }

    @RequestMapping("/toUpdate")
    public String toUpdate(Integer id,Model model){
        Customer customer = customerService.selectById(id);
        model.addAttribute("customer",customer);
        return "customer_update";
    }

    @RequestMapping("/update")
    public String update(Customer customer){
        customerService.update(customer);
        return "redirect:/customer/selectByPage";
    }


    @RequestMapping("/toUpdatec")
    public String toUpdatec(Integer id,Model model){
        Customer customer = customerService.selectById(id);
        model.addAttribute("customer",customer);
        return "customer_updatec";
    }

    @RequestMapping("/updatec")
    public String updatec(Customer customer){
        customerService.update(customer);
        return "redirect:/customer/tointer";
    }

    @RequestMapping("/search")
    public String Search(String name, Model model) {
        if (name != null && name != "") {
            model.addAttribute("name", name);
            List<Customer> search = customerService.searchByname(name);
            model.addAttribute("search", search);
        }
        return "customer_search";
    }

    @RequestMapping("/toAdd")
    public String toAdd() {
        return "customer_add";
    }

    @RequestMapping("/add")
    public String add(Customer customer) {
        customerService.add(customer);
        return "redirect:/customer/selectByPage";
    }

    //  /customer/deleteById?id=23
    @RequestMapping("/deleteById")
    public String deleteById(Integer id){
        customerService.deleteById(id);
        //删除完查找最新数据 重定向
        return "redirect:/customer/selectByPage";
    }

    @RequestMapping("/deleteAll")
    public String deleteAll(Integer[] ids) {
        customerService.deleteAll(ids);
        return "redirect:/customer/selectByPage";
    }

    @RequestMapping("/tointer")
    public String tointer(Model model) {
        List<Customer> list = customerService.selectAll();
        model.addAttribute("list", list);
        //转到commodity_list 界面
        return "customer_interface";
    }

    @RequestMapping("/selectByPage")
    public String selectByPage(@RequestParam(defaultValue = "1") Integer pageNo,
                               @RequestParam(defaultValue = "10") Integer pageSize, Model model) {
        System.out.println("CustomerController.selectByPage");
        PageInfoc pageInfo = customerService.selectByPage(pageNo, pageSize);

        //把pageInfo数据放到内存里面
        model.addAttribute("pageInfo", pageInfo);
        //转发到user_list界面展示
        return "customer_list";
    }


    @RequestMapping("/selectAll")
    public String selectAll(Model model) {
        System.out.println("CustomerController.selectAll");
        // soutm
        List<Customer> list = customerService.selectAll();
        model.addAttribute("list", list);
        //转到customer_list 界面
        return "customer_list";
    }

    @RequestMapping("/selectAll1")
    @ResponseBody
    public List<Customer> selectAll(){
        System.out.println("CustomerController.selectAll");
        // soutm
        System.out.println("CustomerController.selectAll");
        List<Customer> list = customerService.selectAll();

        return list;
    }

    @RequestMapping("/about")
    public String about(){
        return "about";
    }
=======

import java.util.Scanner;

public class PaymentSystem {
    // 定义付款方式常量
    private static final int ALIPAY = 1;
    private static final int WECHATPAY = 2;
    private static final int BANKCARDPAY = 3;

    // 定义付款方式枚举
    enum PaymentMethod {
        ALIPAY,
        WECHATPAY,
        BANKCARDPAY
    }

    public static void main(String[] args) {
        try (Scanner scanner = new Scanner(System.in)) {
            // 模拟商品价格
            double totalPrice = 100.0;

            // 询问用户付款方式
            System.out.println("请选择付款方式：");
            System.out.println("1. 支付宝");
            System.out.println("2. 微信");
            System.out.println("3. 银行卡");
            int paymentMethod = scanner.nextInt();

            // 模拟付款逻辑
            boolean paymentSuccess = false;
            switch (paymentMethod) {
                case ALIPAY:
                    paymentSuccess = aliPay(totalPrice);
                    break;
                case WECHATPAY:
                    paymentSuccess = weChatPay(totalPrice);
                    break;
                case BANKCARDPAY:
                    paymentSuccess = bankCardPay(totalPrice);
                    break;
                default:
                    System.out.println("无效的付款方式！");
            }

            // 结算
            if (paymentSuccess) {
                System.out.println("支付成功，总金额：" + totalPrice + "元");
            } else {
                System.out.println("支付失败，请重新尝试或更换付款方式！");
            }
        }
    }

    // 模拟支付宝支付
    public static boolean aliPay(double amount) {
        // 这里可以加入支付宝支付的逻辑
        System.out.println("支付宝支付：" + amount + "元");
        return true; // 假设支付成功
    }

    // 模拟微信支付
    public static boolean weChatPay(double amount) {
        // 这里可以加入微信支付的逻辑
        System.out.println("微信支付：" + amount + "元");
        return true; // 假设支付成功
    }

    // 模拟银行卡支付
    public static boolean bankCardPay(double amount) {
        // 这里可以加入银行卡支付的逻辑
        System.out.println("银行卡支付：" + amount + "元");
        return true; // 假设支付成功
    }
}
=======

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

// 预约服务类
class BookingService {
    private List<ChargingRobot> chargingRobots;

    public BookingService() {
        chargingRobots = new ArrayList<>();
    }

    // 添加充电机器人
    public void addChargingRobot(ChargingRobot chargingRobot) {
        chargingRobots.add(chargingRobot);
    }

    // 预约充电服务
    public void bookChargingService(String type) {
        for (ChargingRobot chargingRobot : chargingRobots) {
            if (chargingRobot.getType().equals(type) && chargingRobot.isAvailable()) {
                chargingRobot.setAvailable(false);
                System.out.println("预约成功！您已预约了一台" + type + "充电机器人。");
                return;
            }
        }
        System.out.println("抱歉，暂时没有可用的" + type + "充电机器人，请稍后再试或选择其他类型。");
    }
}

public class Main {
    public static void main(String[] args) {
        BookingService bookingService = new BookingService();
        bookingService.addChargingRobot(new ChargingRobot("快充", "A区"));
        bookingService.addChargingRobot(new ChargingRobot("慢充", "B区"));

        Scanner scanner = new Scanner(System.in);

        while (true) {
            System.out.println("欢迎使用充电预约服务");
            System.out.println("1. 预约充电服务");
            System.out.println("2. 退出");

            int choice = scanner.nextInt();
            scanner.nextLine();  // 消耗换行符

            switch (choice) {
                case 1:
                    System.out.println("请选择充电机器人类型（快充/慢充）:");
                    String type = scanner.nextLine();
                    bookingService.bookChargingService(type);
                    break;
                case 2:
                    System.out.println("退出系统。");
                    System.exit(0);
                    break;
                default:
                    System.out.println("无效选项，请重新输入。");
            }
        }
    }
=======
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
