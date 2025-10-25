# Car-Parking-Ticket-System




import java.util.*;
import java.time.*;

class Ticket {
    String ticketId;
    String vehicleNumber;
    LocalDateTime entryTime;

    Ticket(String ticketId, String vehicleNumber, LocalDateTime entryTime) {
        this.ticketId = ticketId;
        this.vehicleNumber = vehicleNumber;
        this.entryTime = entryTime;
    }
}

public class ParkingSystem {
    static Map<String, Ticket> activeTickets = new HashMap<>();
    static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        while (true) {
            System.out.println("\n===== Car Parking Ticket System =====");
            System.out.println("1. Vehicle Entry");
            System.out.println("2. Vehicle Exit");
            System.out.println("3. Show Active Tickets");
            System.out.println("4. Exit");
            System.out.print("Choose option: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // consume newline

            switch (choice) {
                case 1:
                    vehicleEntry();
                    break;
                case 2:
                    vehicleExit();
                    break;
                case 3:
                    showActiveTickets();
                    break;
                case 4:
                    System.out.println("Thank you for using the system!");
                    return;
                default:
                    System.out.println("Invalid choice. Try again.");
            }
        }
    }

    static void vehicleEntry() {
        System.out.print("Enter vehicle number: ");
        String vehicleNumber = scanner.nextLine();
        String ticketId = UUID.randomUUID().toString().substring(0, 8);
        LocalDateTime entryTime = LocalDateTime.now();
        Ticket ticket = new Ticket(ticketId, vehicleNumber, entryTime);
        activeTickets.put(ticketId, ticket);

        System.out.println("\n✅ Ticket Issued");
        System.out.println("Ticket ID: " + ticketId);
        System.out.println("Vehicle Number: " + vehicleNumber);
        System.out.println("Entry Time: " + entryTime);
    }

    static void vehicleExit() {
        System.out.print("Enter Ticket ID: ");
        String ticketId = scanner.nextLine();

        if (!activeTickets.containsKey(ticketId)) {
            System.out.println("❌ Ticket not found.");
            return;
        }

        Ticket ticket = activeTickets.get(ticketId);
        LocalDateTime exitTime = LocalDateTime.now();
        Duration duration = Duration.between(ticket.entryTime, exitTime);
        long totalMinutes = duration.toMinutes();
        long totalHours = (long) Math.ceil(totalMinutes / 60.0); // round up to nearest hour

        double amount = calculateAmount(totalHours);

        System.out.println("\n🧾 Payment Receipt");
        System.out.println("Ticket ID: " + ticket.ticketId);
        System.out.println("Vehicle Number: " + ticket.vehicleNumber);
        System.out.println("Entry Time: " + ticket.entryTime);
        System.out.println("Exit Time: " + exitTime);
        System.out.println("Total Duration: " + totalHours + " hour(s)");
        System.out.println("Total Amount to Pay: $" + amount);

        simulateOnlinePayment(amount);
        activeTickets.remove(ticketId);
    }

    static double calculateAmount(long hours) {
        if (hours <= 1) {
            return 3.0;
        } else if (hours <= 2) {
            return 5.0;
        } else {
            long extraHours = hours - 2;
            return 5.0 + extraHours * 1.0; // $1 fine per extra hour
        }
    }

    static void simulateOnlinePayment(double amount) {
        System.out.print("💳 Proceeding to online payment... ");
        try {
            Thread.sleep(1000); // simulate delay
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Payment of $" + amount + " successful!");
    }

    static void showActiveTickets() {
        if (activeTickets.isEmpty()) {
            System.out.println("🚫 No active tickets.");
            return;
        }

        System.out.println("\n📋 Active Tickets:");
        for (Ticket ticket : activeTickets.values()) {
            System.out.println("Ticket ID: " + ticket.ticketId +
                               " | Vehicle: " + ticket.vehicleNumber +
                               " | Entry Time: " + ticket.entryTime);
        }
    }
}
