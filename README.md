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
}
