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
}
