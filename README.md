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
