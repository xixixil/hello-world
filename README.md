package com.situ.springboot.controller;

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
        List<User> list = userService.selectAll();
        //把list数据放到内存里面
        model.addAttribute("list", list);
        //转发到user_list界面展示
        return "home";
    }
    @RequestMapping("/selectByPage")
    public String selectByPage(@RequestParam(defaultValue = "1") Integer pageNo,
                               @RequestParam(defaultValue = "10") Integer pageSize, Model model) {
        System.out.println("UserController.selectByPage");
        PageInfo pageInfo = userService.selectByPage(pageNo, pageSize);
        //把pageInfo数据放到内存里面
        model.addAttribute("pageInfo", pageInfo);
        //转发到user_list界面展示
        return "user/user-list";
    }
    @RequestMapping("/toUpdate")
    public String toUpdate(Integer id, Model model) {
        User user = userService.selectById(id);
        model.addAttribute("user", user);
        return "user/user-update";
    }

    @RequestMapping("/update")
    public String update(User user) {
        userService.update(user);
        return "redirect:/user/selectByPage";
    }
        @RequestMapping("/toAdd")
    public String toAdd() {
        return "user/user-add";
    }

    @RequestMapping("/add")
    public String add(User user) {
        userService.add(user);
        return "redirect:/user/selectByPage";
    }
    // /user/deleteById?id=23
    @RequestMapping("/deleteById")
    public String deleteById(Integer id) {
        userService.deleteById(id);
        //redirect:重定向，告诉浏览器发送请求 /user/selectAll
        //删除完之后，应该查找展示最新的数据
        return "redirect:/user/selectByPage";
        //return "/user/selectAll";
    }

    @RequestMapping("/toLogin")
    public String toLogin() {
        return "books/userlook";
    }
//修改这个才能变换页面


    @RequestMapping("/login")
    @ResponseBody //返回json格式数据要加这个注解 @ResponseBody
    public Result login(String name, String password, String code, HttpSession session) {
        //先验证验证码对不对，验证码正确再验证用户名密码是否正确
        //如果验证码不对，直接返回错误，后面也不需要执行
//        String codeInSession = (String) session.getAttribute("codeInSession");
//        if (!codeInSession.equalsIgnoreCase(code)) {
//            return Result.error("验证码错误");
//        }
        User user = userService.login(name, password);
        if (user != null) {
            if (user.getStatus() == 1) {
                //这个用户禁用
                return Result.error("该用户已经禁用");
            }
            session.setAttribute("user", user);
            return Result.ok("用户登录成功");
        } else {
                // 登录失败
                return Result.error("用户名或密码错误");
            }

    }
    @RequestMapping("search")
    public String Search(@RequestParam(defaultValue = "1") Integer pageNo,
                         @RequestParam(defaultValue = "100") Integer pageSize,String name, Model model) {
        if (name != null && !name.isEmpty()) {
            model.addAttribute("name", name);
            List<User> search = userService.searchByName(name);
            PageInfo pageInfo = userService.usernameselectByPage(pageNo, pageSize, name);
            model.addAttribute("pageInfo", pageInfo);

            System.out.println(search);
        }

        return "user/user-search";
    }

    @RequestMapping("/logout")
    public String logout(HttpSession session) {
        session.invalidate();
        return "redirect:/user/toLogin";
    }
//    @GetMapping("/toECharts")
//    public String toECharts() {
//        // 处理逻辑
//        return "home"; // 你的视图名
//    }

}


//#2
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
