import java.time.LocalDate;
import java.util.*;
import java.util.stream.Collectors;
import java.util.DoubleSummaryStatistics;

public class WeatherLogger {

    // Weather Log Model
    static class WeatherLog {
        private String city;
        private double temperature;
        private LocalDate date;
        private String condition;

        public WeatherLog(String city, double temperature, LocalDate date, String condition) {
            this.city = city;
            this.temperature = temperature;
            this.date = date;
            this.condition = condition;
        }

        public String getCity() {
            return city;
        }

        public double getTemperature() {
            return temperature;
        }

        public LocalDate getDate() {
            return date;
        }

        public String getCondition() {
            return condition;
        }

        @Override
        public String toString() {
            return city + " | " + temperature + "°C | " + date + " | " + condition;
        }
    }

    // Service to handle CRUD and filtering
    static class WeatherService {
        private final List<WeatherLog> logs = new ArrayList<>();

        // Add a weather log
        public void addLog(WeatherLog log) {
            logs.add(log);
        }

        // Delete a weather log
        public void deleteLog(int index) {
            if (index >= 0 && index < logs.size()) logs.remove(index);
        }

        // Update a weather log
        public void updateLog(int index, WeatherLog updatedLog) {
            if (index >= 0 && index < logs.size()) logs.set(index, updatedLog);
        }

        // Get all weather logs
        public List<WeatherLog> getAllLogs() {
            return logs;
        }

        // Filter logs by city
        public List<WeatherLog> filterByCity(String city) {
            return logs.stream()
                    .filter(log -> log.getCity().equalsIgnoreCase(city))
                    .collect(Collectors.toList());
        }

        // Filter logs by date range
        public List<WeatherLog> filterByDateRange(LocalDate from, LocalDate to) {
            return logs.stream()
                    .filter(log -> !log.getDate().isBefore(from) && !log.getDate().isAfter(to))
                    .collect(Collectors.toList());
        }

        // Print summary by city (max, min, average)
        public void printSummaryByCity(String city) {
            List<WeatherLog> filtered = filterByCity(city);
            if (filtered.isEmpty()) {
                System.out.println("No records for city: " + city);
                return;
            }

            DoubleSummaryStatistics stats = filtered.stream()
                    .mapToDouble(WeatherLog::getTemperature)
                    .summaryStatistics();

            System.out.println("Summary for " + city + ":");
            System.out.println("Max Temp: " + stats.getMax() + "°C");
            System.out.println("Min Temp: " + stats.getMin() + "°C");
            System.out.println("Avg Temp: " + stats.getAverage() + "°C");
        }
    }

    // Main Method (User Interface)
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        WeatherService service = new WeatherService();

        while (true) {
            System.out.println("\nWeather Logger Menu:");
            System.out.println("1. Add Log\n2. View Logs\n3. Delete Log\n4. Update Log");
            System.out.println("5. Filter by City\n6. Filter by Date Range\n7. Summary by City\n8. Exit");
            System.out.print("Choose an option: ");
            int choice = Integer.parseInt(scanner.nextLine());

            switch (choice) {
                case 1 -> {
                    System.out.print("City: ");
                    String city = scanner.nextLine();
                    System.out.print("Temperature: ");
                    double temp = Double.parseDouble(scanner.nextLine());
                    System.out.print("Date (YYYY-MM-DD): ");
                    LocalDate date = LocalDate.parse(scanner.nextLine());
                    System.out.print("Condition: ");
                    String condition = scanner.nextLine();
                    service.addLog(new WeatherLog(city, temp, date, condition));
                }
                case 2 -> service.getAllLogs().forEach(System.out::println);
                case 3 -> {
                    System.out.print("Enter log index to delete: ");
                    int index = Integer.parseInt(scanner.nextLine());
                    service.deleteLog(index);
                }
                case 4 -> {
                    System.out.print("Enter log index to update: ");
                    int index = Integer.parseInt(scanner.nextLine());
                    System.out.print("New City: ");
                    String city = scanner.nextLine();
                    System.out.print("New Temp: ");
                    double temp = Double.parseDouble(scanner.nextLine());
                    System.out.print("New Date (YYYY-MM-DD): ");
                    LocalDate date = LocalDate.parse(scanner.nextLine());
                    System.out.print("New Condition: ");
                    String condition = scanner.nextLine();
                    service.updateLog(index, new WeatherLog(city, temp, date, condition));
                }
                case 5 -> {
                    System.out.print("Enter city to filter: ");
                    service.filterByCity(scanner.nextLine()).forEach(System.out::println);
                }
                case 6 -> {
                    System.out.print("From Date (YYYY-MM-DD): ");
                    LocalDate from = LocalDate.parse(scanner.nextLine());
                    System.out.print("To Date (YYYY-MM-DD): ");
                    LocalDate to = LocalDate.parse(scanner.nextLine());
                    service.filterByDateRange(from, to).forEach(System.out::println);
                }
                case 7 -> {
                    System.out.print("Enter city for summary: ");
                    service.printSummaryByCity(scanner.nextLine());
                }
                case 8 -> {
                    System.out.println("Exiting...");
                    return;
                }
                default -> System.out.println("Invalid choice!");
            }
        }
    }
}
