#include <stdio.h>
#include <stdbool.h>
#include <stdlib.h>

#define ESPRESSO 1
#define CAPPUCCINO 2
#define MOCHA 3
#define EXIT 4
#define ADMIN_MODE 5

#define INITIAL_COFFEE_BEANS 50
#define INITIAL_WATER_ML 200
#define INITIAL_MILK_ML 150
#define INITIAL_SYRUP_ML 50

#define COFFEE_BEANS 8

#define WATER_ML_ESPRESSO 30
#define WATER_ML_CAPPUCCINO 30
#define WATER_ML_MOCHA 39

#define MILK_ML_CAPPUCCINO 70
#define MILK_ML_MOCHA 160

#define CHOCOLATE_SYRUP_ML_MOCHA 30

#define DEFAULT_PRICE_ESPRESSO 3.5
#define DEFAULT_PRICE_CAPPUCCINO 4.5
#define DEFAULT_PRICE_MOCHA 5.5

int current_coffee_beans = INITIAL_COFFEE_BEANS;
int current_water_ml = INITIAL_WATER_ML;
int current_milk_ml = INITIAL_MILK_ML;
int current_chocolate_syrup_ml = INITIAL_SYRUP_ML;

double total_earnings = 0.0;
double price_espresso = DEFAULT_PRICE_ESPRESSO;
double price_cappuccino = DEFAULT_PRICE_CAPPUCCINO;
double price_mocha = DEFAULT_PRICE_MOCHA;

bool check_ingredients(int coffee_type);
bool process_payment(double price);
void admin_mode();
void replenish_ingredients();
void change_coffee_prices();
void collect_earnings();
void display_earnings_and_ingredients();

int main() {
    while (1) {
        if (coffee_selection() == EXIT) {
            break;
        }
    }
    printf("Thank you for using the coffee machine!\n");
    return 0;
}

int coffee_selection() {
    int customer_button;
    char confirm;

    printf("\nChoose a coffee\n");
    printf("Enter 1 for Espresso     %.1f AED\n", price_espresso);
    printf("Enter 2 for Cappuccino   %.1f AED\n", price_cappuccino);
    printf("Enter 3 for Mocha        %.1f AED\n", price_mocha);
    printf("Enter 4 to Exit\n");
    printf("Enter 5 for Admin Mode\n");
    scanf("%d", &customer_button);

    if (customer_button == ADMIN_MODE) {
        admin_mode();
        return 0;
    } else if (customer_button == EXIT) {
        return EXIT;
    }

    if (customer_button < 1 || customer_button > 3) {
        printf("Invalid selection. Please choose a valid coffee option.\n");
        return 0;
    }

    printf("Confirm your selection (y/n): ");
    scanf(" %c", &confirm);

    if (confirm == 'n' || confirm == 'N') {
        printf("Returning to coffee selection...\n\n");
        return 0;
    }

    double price = (customer_button == ESPRESSO) ? price_espresso :
                   (customer_button == CAPPUCCINO) ? price_cappuccino :
                   price_mocha;

    if (check_ingredients(customer_button)) {
        if (customer_button == ESPRESSO) {
            current_coffee_beans -= COFFEE_BEANS;
            current_water_ml -= WATER_ML_ESPRESSO;
        } else if (customer_button == CAPPUCCINO) {
            current_coffee_beans -= COFFEE_BEANS;
            current_water_ml -= WATER_ML_CAPPUCCINO;
            current_milk_ml -= MILK_ML_CAPPUCCINO;
        } else if (customer_button == MOCHA) {
            current_coffee_beans -= COFFEE_BEANS;
            current_water_ml -= WATER_ML_MOCHA;
            current_milk_ml -= MILK_ML_MOCHA;
            current_chocolate_syrup_ml -= CHOCOLATE_SYRUP_ML_MOCHA;
        }

        if (process_payment(price)) {
            total_earnings += price;
            printf("Thank you! Your coffee is being prepared.\n");
        } else {
            printf("Payment unsuccessful. Returning to coffee selection...\n");
        }
    } else {
        printf("Not enough ingredients. Returning to coffee selection...\n");
    }

    return 0;
}

bool check_ingredients(int coffee_type) {
    if (coffee_type == ESPRESSO) {
        return (current_coffee_beans >= COFFEE_BEANS && current_water_ml >= WATER_ML_ESPRESSO);
    } else if (coffee_type == CAPPUCCINO) {
        return (current_coffee_beans >= COFFEE_BEANS &&
                current_water_ml >= WATER_ML_CAPPUCCINO &&
                current_milk_ml >= MILK_ML_CAPPUCCINO);
    } else if (coffee_type == MOCHA) {
        return (current_coffee_beans >= COFFEE_BEANS &&
                current_water_ml >= WATER_ML_MOCHA &&
                current_milk_ml >= MILK_ML_MOCHA &&
                current_chocolate_syrup_ml >= CHOCOLATE_SYRUP_ML_MOCHA);
    }
    return false;
}

bool process_payment(double price) {
    double total_paid = 0.0;
    double coin;

    printf("Enter coins (1.0 or 0.5 AED) to pay %.2f AED:\n", price);

    while (total_paid < price) {
        printf("Enter coin: ");
        scanf("%lf", &coin);

        if (coin == 1.0 || coin == 0.5) {
            total_paid += coin;
            printf("Total paid: %.1f AED\n", total_paid);
        } else {
            printf("Invalid coin. Use 1.0 or 0.5 AED.\n");
        }
    }
    printf("Payment successful!\n");
    return true;
}

void admin_mode() {
    int password;

    printf("Enter admin password: ");
    scanf("%d", &password);

    if (password == 1234) {
        int choice;
        do {
            printf("\nAdmin Menu:\n");
            printf("1. Display earnings and ingredients\n");
            printf("2. Replenish ingredients\n");
            printf("3. Change coffee prices\n");
            printf("4. Exit Admin Mode\n");
            printf("Choose an option: ");
            scanf("%d", &choice);

            if (choice == 1) {
                display_earnings_and_ingredients();
            } else if (choice == 2) {
                replenish_ingredients();
            } else if (choice == 3) {
                change_coffee_prices();
            } else if (choice != 4) {
                printf("Invalid choice. Try again.\n");
            }
        } while (choice != 4);
    } else {
        printf("Incorrect password. Returning to coffee selection...\n");
    }
}

void replenish_ingredients() {
    int added_beans = 50 + rand() % 51;
    int added_water = 300 + rand() % 201;
    int added_milk = 300 + rand() % 201;
    int added_syrup = 100 + rand() % 101;

    current_coffee_beans += added_beans;
    current_water_ml += added_water;
    current_milk_ml += added_milk;
    current_chocolate_syrup_ml += added_syrup;

    printf("Ingredients replenished:\n");
    printf("Coffee beans: %d g\n", current_coffee_beans);
    printf("Water: %d ml\n", current_water_ml);
    printf("Milk: %d ml\n", current_milk_ml);
    printf("Chocolate syrup: %d ml\n", current_chocolate_syrup_ml);
}

void change_coffee_prices() {
    printf("Enter new price for Espresso: ");
    scanf("%lf", &price_espresso);
    printf("Enter new price for Cappuccino: ");
    scanf("%lf", &price_cappuccino);
    printf("Enter new price for Mocha: ");
    scanf("%lf", &price_mocha);
    printf("Prices updated successfully.\n");
}

void display_earnings_and_ingredients() {
    printf("\nIngredients:\n");
    printf("Coffee beans: %d g\n", current_coffee_beans);
    printf("Water: %d ml\n", current_water_ml);
    printf("Milk: %d ml\n", current_milk_ml);
    printf("Chocolate syrup: %d ml\n", current_chocolate_syrup_ml);
    printf("Total earnings: %.2f AED\n", total_earnings);

    char collect;
    printf("Collect earnings? (y/n): ");
    scanf(" %c", &collect);

    if (collect == 'y' || collect == 'Y') {
        collect_earnings();
    } else {
        printf("Earnings not collected.\n");
    }
}

void collect_earnings() {
    printf("Collected %.2f AED\n", total_earnings);
    total_earnings = 0.0;
}
