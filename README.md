import java.util.Scanner;

public class PaymentSystem {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

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
            case 1:
                paymentSuccess = aliPay(totalPrice);
                break;
            case 2:
                paymentSuccess = weChatPay(totalPrice);
                break;
            case 3:
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

        scanner.close();
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
