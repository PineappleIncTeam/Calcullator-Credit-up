import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.net.http.HttpHeaders;
import java.util.Scanner;

public class CreditCalculator {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Введите сумму кредита: ");
        double loanAmount = scanner.nextDouble();

        System.out.print("Введите годовую процентную ставку: ");
        double annualInterestRate = scanner.nextDouble();

        System.out.print("Введите срок кредита (в месяцах): ");
        int loanTermInMonths = scanner.nextInt();

        // Формирование запроса к REST API для расчета кредита
        String apiUrl = "https://api.example.com/credit-calculator";
        String requestBody = String.format("{\"loanAmount\": %.2f, \"annualInterestRate\": %.2f, \"loanTermInMonths\": %d}",
                loanAmount, annualInterestRate, loanTermInMonths);

        HttpClient client = HttpClient.newHttpClient();
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(apiUrl))
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(requestBody))
                .build();

        try {
            // Отправка запроса и получение ответа от REST API
            HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

            // Обработка ответа
            int statusCode = response.statusCode();
            if (statusCode == 200) {
                String responseBody = response.body();
                System.out.println("Ответ от сервера: " + responseBody);
                // Дополнительная обработка ответа и вывод результатов
            } else {
                System.out.println("Ошибка при отправке запроса. Код ошибки: " + statusCode);
            }
        } catch (Exception e) {
            System.out.println("Ошибка при отправке запроса: " + e.getMessage());
        }

        scanner.close();
    }
}


