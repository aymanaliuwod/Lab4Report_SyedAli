#include <stdio.h>
#include <math.h>
#include <stdbool.h>
#include <string.h>
#include <stdlib.h>
#include <time.h>

#define COFFEE_ESPRESSO 1
#define COFFEE_CAPPUCCINO 2
#define COFFEE_MOCHA 3
#define ACTION_EXIT 4
#define ACTION_ADMIN_MODE 5

#define INITIAL_BEANS 50
#define INITIAL_WATER 200
#define INITIAL_MILK 150
#define INITIAL_SYRUP 50

#define BEANS_PER_COFFEE 8

#define WATER_ESPRESSO 30
#define WATER_CAPPUCCINO 30
#define WATER_MOCHA 39

#define MILK_CAPPUCCINO 70
#define MILK_MOCHA 160

#define SYRUP_MOCHA 30

#define PRICE_ESPRESSO_DEFAULT 3.5
#define PRICE_CAPPUCCINO_DEFAULT 4.5
#define PRICE_MOCHA_DEFAULT 5.5

int beans_current = INITIAL_BEANS;
int water_current_ml = INITIAL_WATER;
int milk_current_ml = INITIAL_MILK;
int syrup_current_ml = INITIAL_SYRUP;

double earnings_total = 0.0;
double price_espresso_current = PRICE_ESPRESSO_DEFAULT;
double price_cappuccino_current = PRICE_CAPPUCCINO_DEFAULT;
double price_mocha_current = PRICE_MOCHA_DEFAULT;

bool check_ingredients_availability(int coffee_type);
bool handle_payment(double price);
void enter_admin_mode();
void refill_ingredients();
void update_coffee_prices();
void retrieve_earnings();
void show_earnings_and_ingredients();

int main() {
    srand(time(0));

    while (1) {
        if (select_coffee() == ACTION_EXIT) {
            break;
        }
    }

    printf("Thank you for using the coffee machine!\n");
    return 0;
}

int select_coffee() {
    int customer_choice;
    char confirmation;

    printf("\nChoose a coffee\n");
    printf("Enter 1 for Espresso     %.1f AED\n", price_espresso_current);
    printf("Enter 2 for Cappuccino   %.1f AED\n", price_cappuccino_current);
    printf("Enter 3 for Mocha        %.1f AED\n", price_mocha_current);
    printf("Enter 4 to Exit\n");
    printf("Enter 5 for Admin Mode\n");
    scanf("%d", &customer_choice);

    if (customer_choice == ACTION_ADMIN_MODE) {
        enter_admin_mode();
        return 0;
    } else if (customer_choice == ACTION_EXIT) {
        return ACTION_EXIT;
    }

    if (customer_choice == COFFEE_ESPRESSO) {
        printf("You selected Espresso.\n");
    } else if (customer_choice == COFFEE_CAPPUCCINO) {
        printf("You selected Cappuccino.\n");
    } else if (customer_choice == COFFEE_MOCHA) {
        printf("You selected Mocha.\n");
    } else {
        printf("Invalid selection. Please choose a valid coffee option.\n");
        return 0;
    }

    printf("Confirm your selection (y/n): ");
    scanf(" %c", &confirmation);

    if (confirmation == 'n' || confirmation == 'N') {
        printf("Returning to coffee selection...\n\n");
        return 0;
    } else if (confirmation == 'y' || confirmation == 'Y') {
        if (check_ingredients_availability(customer_choice)) {
            double price;
            if (customer_choice == COFFEE_ESPRESSO) {
                beans_current -= BEANS_PER_COFFEE;
                water_current_ml -= WATER_ESPRESSO;
                price = price_espresso_current;
            } else if (customer_choice == COFFEE_CAPPUCCINO) {
                beans_current -= BEANS_PER_COFFEE;
                water_current_ml -= WATER_CAPPUCCINO;
                milk_current_ml -= MILK_CAPPUCCINO;
                price = price_cappuccino_current;
            } else if (customer_choice == COFFEE_MOCHA) {
                beans_current -= BEANS_PER_COFFEE;
                water_current_ml -= WATER_MOCHA;
                milk_current_ml -= MILK_MOCHA;
                syrup_current_ml -= SYRUP_MOCHA;
                price = price_mocha_current;
            }

            if (handle_payment(price)) {
                earnings_total += price;
                printf("Thank you! Your coffee is being prepared.\n");
            } else {
                printf("Payment was unsuccessful. Returning to coffee selection...\n\n");
            }
        } else {
            printf("Not enough ingredients\nReturning to coffee selection...\n\n");
        }
    } else {
        printf("Invalid confirmation input. Returning to coffee selection.\n\n");
    }

    return 0;
}

bool check_ingredients_availability(int coffee_type) {
    if (coffee_type == COFFEE_ESPRESSO) {
        return (beans_current >= BEANS_PER_COFFEE &&
                water_current_ml >= WATER_ESPRESSO);
    } else if (coffee_type == COFFEE_CAPPUCCINO) {
        return (beans_current >= BEANS_PER_COFFEE &&
                water_current_ml >= WATER_CAPPUCCINO &&
                milk_current_ml >= MILK_CAPPUCCINO);
    } else if (coffee_type == COFFEE_MOCHA) {
        return (beans_current >= BEANS_PER_COFFEE &&
                water_current_ml >= WATER_MOCHA &&
                milk_current_ml >= MILK_MOCHA &&
                syrup_current_ml >= SYRUP_MOCHA);
    }
    return false;
}

bool handle_payment(double price) {
    double total_paid = 0.0;
    double coin;

    printf("Please enter coins (1.0 or 0.5 AED) to pay for your coffee (total price: %.2f AED):\n", price);

    while (total_paid < price) {
        printf("Enter coin (1.0 or 0.5 AED): ");
        scanf("%lf", &coin);

        if (coin == 1.0 || coin == 0.5) {
            total_paid += coin;
            printf("Total paid: %.1f AED\n", total_paid);
        } else {
            printf("Invalid coin. Please enter 1.0 or 0.5 AED.\n");
        }
    }

    printf("Payment successful! Total paid: %.1f AED\n", total_paid);
    return true;
}

void enter_admin_mode() {
    char admin_password[20];

    printf("Enter admin password: ");
    scanf("%s", admin_password);

    if (strcmp(admin_password, "admin123") == 0) {
        int admin_choice;
        do {
            printf("\nAdmin Mode Menu:\n");
            printf("1. Display ingredients and total earnings\n");
            printf("2. Replenish ingredients\n");
            printf("3. Change coffee prices\n");
            printf("4. Exit Admin Mode\n");
            printf("Choose an option: ");
            scanf("%d", &admin_choice);

            switch (admin_choice) {
                case 1:
                    show_earnings_and_ingredients();
                    break;
                case 2:
                    refill_ingredients();
                    break;
                case 3:
                    update_coffee_prices();
                    break;
                case 4:
                    printf("Exiting Admin Mode...\n");
                    break;
                default:
                    printf("Invalid option. Please choose again.\n");
            }
        } while (admin_choice != 4);
    } else {
        printf("Incorrect password. Returning to coffee selection...\n");
    }
}

void refill_ingredients() {
    int beans_added = 50 + rand() % 51;
    int water_added = 300 + rand() % 201;
    int milk_added = 300 + rand() % 201;
    int syrup_added = 100 + rand() % 101;

    beans_current += beans_added;
    water_current_ml += water_added;
    milk_current_ml += milk_added;
    syrup_current_ml += syrup_added;

    printf("Ingredients replenished:\n");
    printf("  Coffee beans added: %d g\n", beans_added);
    printf("  Water added: %d ml\n", water_added);
    printf("  Milk added: %d ml\n", milk_added);
    printf("  Chocolate syrup added: %d ml\n", syrup_added);
}

void update_coffee_prices() {
    double new_price;

    printf("Current prices:\n");
    printf("  Espresso: %.2f AED\n", price_espresso_current);
    printf("  Cappuccino: %.2f AED\n", price_cappuccino_current);
    printf("  Mocha: %.2f AED\n", price_mocha_current);

    printf("Enter new price for Espresso: ");
    scanf("%lf", &new_price);
    price_espresso_current = new_price;

    printf("Enter new price for Cappuccino: ");
    scanf("%lf", &new_price);
    price_cappuccino_current = new_price;

    printf("Enter new price for Mocha: ");
    scanf("%lf", &new_price);
    price_mocha_current = new_price;

    printf("Prices updated successfully!\n");
}

void show_earnings_and_ingredients() {
    printf("\nIngredients remaining:\n");
    printf("  Coffee beans: %d g\n", beans_current);
    printf("  Water: %d ml\n", water_current_ml);
    printf("  Milk: %d ml\n", milk_current_ml);
    printf("  Chocolate syrup: %d ml\n", syrup_current_ml);
    printf("Total earnings: %.2f AED\n", earnings_total);

    char collect;
    printf("Would you like to collect the earnings? (y/n): ");
    scanf(" %c", &collect);

    if (collect == 'y' || collect == 'Y') {
        retrieve_earnings();
    } else {
        printf("Earnings not collected.\n");
    }
}

void retrieve_earnings() {
    if (earnings_total > 0) {
        printf("Collecting earnings: %.2f AED\nRemember to collect earnings\n", earnings_total);
        earnings_total = 0.0;
    } else {
        printf("No earnings to collect.\n");
    }
}
