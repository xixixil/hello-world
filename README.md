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

    @RequestMapping("/logout")
    public String logout(HttpSession session) {
        session.invalidate();
        return "redirect:/user/toLogin";
    }

    // 其他方法不变...
}
