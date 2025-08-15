import java.util.*;

class User {
    String username;
    String password;
    String name;

    public User(String username, String password, String name) {
        this.username = username;
        this.password = password;
        this.name = name;
    }

    public boolean authenticate(String password) {
        return this.password.equals(password);
    }

    public void updateProfile(String name) {
        this.name = name;
        System.out.println("Success: Profile updated successfully!");
    }

    public void updatePassword(String newPassword) {
        this.password = newPassword;
        System.out.println("Success: Password updated successfully!");
    }
}

class Question {
    String question;
    String[] options;
    char correctAnswer;

    public Question(String question, String[] options, char correctAnswer) {
        this.question = question;
        this.options = options;
        this.correctAnswer = correctAnswer;
    }

    public void display() {
        System.out.println(question);
        for (int i = 0; i < options.length; i++) {
            System.out.println((char)('A' + i) + ". " + options[i]);
        }
    }
}

public class OnlineExamination {
    static Scanner sc = new Scanner(System.in);
    static Map<String, User> users = new HashMap<>();
    static User currentUser;
    static List<Question> questions = new ArrayList<>();

    public static void main(String[] args) {
        // Demo users
        users.put("student1", new User("student1", "1234", "John Doe"));
        users.put("student2", new User("student2", "5678", "Jane Smith"));

        // Sample Questions
        questions.add(new Question("Which language is used for Android development?",
                new String[]{"Java", "Python", "C++", "HTML"}, 'A'));
        questions.add(new Question("Which company developed Java?",
                new String[]{"Microsoft", "Sun Microsystems", "Apple", "Google"}, 'B'));
        questions.add(new Question("Which keyword is used to inherit a class in Java?",
                new String[]{"implement", "extends", "inherits", "super"}, 'B'));
        questions.add(new Question("What is the size of an int in Java?",
                new String[]{"2 bytes", "4 bytes", "8 bytes", "Depends on OS"}, 'B'));
        questions.add(new Question("Which of the following is not a Java feature?",
                new String[]{"Object-oriented", "Use of pointers", "Portable", "Dynamic"}, 'B'));
        questions.add(new Question("What does the `static` keyword mean in Java?",
                new String[]{"The method can be called without creating an instance of the class", "The method is final", "The method is abstract", "The method is volatile"}, 'A'));
        questions.add(new Question("What is the default value of a boolean variable?",
                new String[]{"true", "false", "0", "null"}, 'B'));
        questions.add(new Question("Which collection class allows duplicate elements?",
                new String[]{"Set", "List", "Map", "All of the above"}, 'B'));
        questions.add(new Question("What is the root class of all classes in Java?",
                new String[]{"java.lang.Class", "java.lang.System", "java.lang.Object", "java.lang.Root"}, 'C'));
        questions.add(new Question("Which of these is used to handle exceptions?",
                new String[]{"try-catch", "throws", "finally", "All of the above"}, 'D'));

        System.out.println("===== ONLINE EXAMINATION SYSTEM =====");

        while (true) {
            System.out.println("\n1. Login");
            System.out.println("2. Register");
            System.out.println("3. Exit");
            System.out.print("Choose an option: ");
            String choice = sc.nextLine();

            switch (choice) {
                case "1":
                    if (login()) {
                        System.out.println(getGreeting() + "! Login Successful. Welcome, " + currentUser.name + ".");
                        mainMenu();
                    } else {
                        System.out.println("Error: Invalid username or password. Please try again.");
                    }
                    break;
                case "2":
                    register();
                    break;
                case "3":
                    System.out.println("Thank you for using the system. Goodbye!");
                    return;
                default:
                    System.out.println("Warning: Invalid option. Please try again.");
            }
        }
    }

    private static boolean login() {
        System.out.print("Enter Username: ");
        String username = sc.nextLine();

        User user = users.get(username);
        if (user == null) {
            return false;
        }

        System.out.print("Enter Password: ");
        String password = sc.nextLine();

        if (user.authenticate(password)) {
            currentUser = user;
            return true;
        }
        return false;
    }

    private static void register() {
        System.out.println("\n--- New User Registration ---");
        System.out.print("Enter a new username: ");
        String username = sc.nextLine();

        if (users.containsKey(username)) {
            System.out.println("Error: Username already exists. Please choose a different one.");
            return;
        }

        System.out.print("Enter your full name: ");
        String name = sc.nextLine();

        System.out.print("Enter a password: ");
        String password = sc.nextLine();

        users.put(username, new User(username, password, name));
        System.out.println("Success: Registration successful! You can now log in.");
    }

    private static void mainMenu() {
        while (true) {
            System.out.println("\n--- Main Menu ---");
            System.out.println("1. Update Profile");
            System.out.println("2. Update Password");
            System.out.println("3. Start Exam");
            System.out.println("4. Logout");
            System.out.print("Enter choice: ");
            String choice = sc.nextLine();

            switch (choice) {
                case "1":
                    System.out.print("Enter new name: ");
                    String newName = sc.nextLine();
                    currentUser.updateProfile(newName);
                    break;
                case "2":
                    System.out.print("Enter new password: ");
                    String newPassword = sc.nextLine();
                    currentUser.updatePassword(newPassword);
                    break;
                case "3":
                    startExam();
                    break;
                case "4":
                    System.out.println("\nSuccess: Logged out successfully!");
                    System.out.println("Thank you for using the Online Examination System.");
                    return;
                default:
                    System.out.println("Warning: Invalid choice.");
            }
        }
    }

    private static void startExam() {
        Map<Integer, Character> answers = new HashMap<>();
        int score = 0;
        long startTime = System.currentTimeMillis();
        int timeLimitSeconds = 300; // 5 minute timer

        System.out.println("\n--- Exam Started ---");
        for (int i = 0; i < questions.size(); i++) {
            long elapsedTime = (System.currentTimeMillis() - startTime) / 1000;
            if (elapsedTime >= timeLimitSeconds) {
                System.out.println("\nTime is up! Auto-submitting...");
                break;
            }

            System.out.println("\nQuestion " + (i + 1) + ":");
            questions.get(i).display();
            System.out.print("Your answer (A/B/C/D): ");
            String ans = sc.nextLine().toUpperCase();
            if (!ans.isEmpty()) {
                answers.put(i, ans.charAt(0));
            }
        }

        // Check answers
        for (int i = 0; i < questions.size(); i++) {
            if (answers.containsKey(i) && answers.get(i) == questions.get(i).correctAnswer) {
                score++;
            }
        }

        System.out.println("\nExam Finished!");
        System.out.println("Your Score: " + score + "/" + questions.size());
    }

    private static String getGreeting() {
        Calendar cal = Calendar.getInstance();
        int hour = cal.get(Calendar.HOUR_OF_DAY); // Use Calendar for broader compatibility
        if (hour >= 5 && hour < 12) {
            return "Good Morning";
        } else if (hour >= 12 && hour < 17) {
            return "Good Afternoon";
        } else if (hour >= 17 && hour < 21) {
            return "Good Evening";
        } else {
            return "Good Night";
        }
    }
}
