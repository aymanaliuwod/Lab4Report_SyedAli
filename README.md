//Question 1

#include <stdio.h>
#include <stdbool.h>

#define SIZE 10

bool isValid(const int arr[], int length, int pos) {
    return (pos >= 0 && pos < length);
}

void remove_element(int arr[], int length, int pos) {
    if (!isValid(arr, length, pos)) {
        printf("Error: Index %d is out of range.\n", pos);
        return;
    }
    for (int i = pos; i < length - 1; i++) {
        arr[i] = arr[i + 1];
    }
    arr[length - 1] = 0;
}

void insert_element(int arr[], int length, int pos, int value) {
    if (!isValid(arr, length, pos)) {
        printf("Error: Index %d is out of range.\n", pos);
        return;
    }
    for (int i = length - 1; i > pos; i--) {
        arr[i] = arr[i - 1];
    }
    arr[pos] = value;
}

void reshape(const int arr[], int length, int nRows, int nCols, int arr2d[nRows][nCols]) {
    if (length != nRows * nCols) {
        printf("Error: Array size does not match matrix dimensions.\n");
        return;
    }
    int k = 0;
    for (int j = 0; j < nCols; j++) {
        for (int i = 0; i < nRows; i++) {
            arr2d[i][j] = arr[k++];
        }
    }
}

void trans_matrix(int nRows, int nCols, const int mat[nRows][nCols], int mat_transp[nCols][nRows]) {
    for (int i = 0; i < nRows; i++) {
        for (int j = 0; j < nCols; j++) {
            mat_transp[j][i] = mat[i][j];
        }
    }
}

bool found_duplicate(int arr[], int length) {
    for (int i = 0; i < length - 1; i++) {
        for (int j = i + 1; j < length; j++) {
            if (arr[i] == arr[j]) {
                return true;
            }
        }
    }
    return false;
}

int main() {
    int arr[SIZE] = {10, 20, 30, 40, 50, 0, 0, 0, 0, 0};
    int arr2d[2][5];
    int transposed[5][2];

    printf("Testing isValid:\n");
    printf("Position 2 is %s\n", isValid(arr, SIZE, 2) ? "valid" : "invalid");
    printf("Position 10 is %s\n", isValid(arr, SIZE, 10) ? "valid" : "invalid");

    printf("\nTesting remove_element:\n");
    printf("Array before removing element at position 2:\n");
    for (int i = 0; i < SIZE; i++) printf("%d ", arr[i]);
    printf("\n");

    remove_element(arr, SIZE, 2);
    printf("Array after removing element at position 2:\n");
    for (int i = 0; i < SIZE; i++) printf("%d ", arr[i]);
    printf("\n");

    int arr_reset[SIZE] = {10, 20, 30, 40, 50, 0, 0, 0, 0, 0};
    for (int i = 0; i < SIZE; i++) arr[i] = arr_reset[i];

    printf("\nTesting insert_element:\n");
    printf("Array before inserting 80 at position 2:\n");
    for (int i = 0; i < SIZE; i++) printf("%d ", arr[i]);
    printf("\n");

    insert_element(arr, SIZE, 2, 80);
    printf("Array after inserting 80 at position 2:\n");
    for (int i = 0; i < SIZE; i++) printf("%d ", arr[i]);
    printf("\n");

    printf("\nTesting reshape:\n");
    reshape(arr, 10, 2, 5, arr2d);
    printf("Reshaped 2D array:\n");
    for (int i = 0; i < 2; i++) {
        for (int j = 0; j < 5; j++) {
            printf("%d ", arr2d[i][j]);
        }
        printf("\n");
    }

    printf("\nTesting trans_matrix:\n");
    trans_matrix(2, 5, arr2d, transposed);
    printf("Transposed 2D array:\n");
    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 2; j++) {
            printf("%d ", transposed[i][j]);
        }
        printf("\n");
    }

    printf("\nTesting found_duplicate:\n");
    bool hasDuplicate = found_duplicate(arr, SIZE);
    printf("Array has duplicates: %s\n", hasDuplicate ? "Yes" : "No");

    return 0;
}


//Question 2

#include <stdio.h>

#define INITIAL_BALANCE 1000
#define MAX_TRANSACTIONS 10

int main()
{
    int balance = INITIAL_BALANCE;
    int transactions[MAX_TRANSACTIONS] = {200, -150, -500, -400, -50, -200, 300};
    int tobeprocessed[MAX_TRANSACTIONS];
    int tobeprocessed_count = 0;

    printf("Starting balance: %d AED\n", balance);

    for (int i = 0; i < MAX_TRANSACTIONS; i++) {
        if (transactions[i] == 0) break;

        int transaction = transactions[i];

        if (transaction < 0 && balance + transaction < 0) {
            printf("Skipped withdrawal of %d AED: Insufficient funds.\n", -transaction);
            tobeprocessed[tobeprocessed_count++] = transaction;
        } else {
            balance += transaction;
            printf("Processed transaction: %d AED, New balance: %d AED\n", transaction, balance);

            if (balance == 0) {
                printf("Balance reached zero. Stopping further transactions.\n");
                for (int j = i + 1; j < MAX_TRANSACTIONS; j++) {
                    if (transactions[j] == 0) break;
                    tobeprocessed[tobeprocessed_count++] = transactions[j];
                }
                break;
            }
        }
    }

    printf("\nFinal balance: %d AED\n", balance);
    printf("Transactions to be processed later:\n");
    for (int i = 0; i < tobeprocessed_count; i++) {
        printf("%d ", tobeprocessed[i]);
    }
    printf("\n");

    return 0;
}

//Question 3

#include <stdio.h>
#include <string.h>
#include <stdbool.h>

#define NUM_TEAMS 20
#define SQUAD_SIZE 53

typedef struct {
    int day;
    int month;
    int year;
} Date;

typedef struct {
    char name[26];
    int kitNumber;
    char club[30];
    Date birthDate;
    char position[20];
} Player;

typedef struct {
    char teamName[21];
    Player players[SQUAD_SIZE];
    int activeSize;
} Team;

Team league[NUM_TEAMS];
int enrolledTeams = 0;

void display_menu() {
    printf("\nMenu:\n");
    printf("1. Enroll Club\n");
    printf("2. Add Player\n");
    printf("3. Search and Update Player\n");
    printf("4. Display Club Statistics\n");
    printf("0. Exit\n");
}

void enroll_club() {
    if (enrolledTeams >= NUM_TEAMS) {
        printf("Maximum number of teams reached.\n");
        return;
    }
    printf("Enter club name: ");
    scanf(" %[^\n]", league[enrolledTeams].teamName);
    league[enrolledTeams].activeSize = 0;
    enrolledTeams++;
    printf("Club enrolled successfully.\n");
}

bool is_duplicate(Player players[], int count, char name[], int kitNumber) {
    for (int i = 0; i < count; i++) {
        if (strcmp(players[i].name, name) == 0 || players[i].kitNumber == kitNumber) {
            return true;
        }
    }
    return false;
}

void add_player() {
    if (enrolledTeams == 0) {
        printf("No clubs available. Enroll a club first.\n");
        return;
    }

    printf("Select a club by number:\n");
    for (int i = 0; i < enrolledTeams; i++) {
        printf("%d. %s\n", i + 1, league[i].teamName);
    }

    int teamIndex;
    scanf("%d", &teamIndex);
    teamIndex--;

    if (teamIndex < 0 || teamIndex >= enrolledTeams || league[teamIndex].activeSize >= SQUAD_SIZE) {
        printf("Invalid selection or team is full.\n");
        return;
    }

    Player newPlayer;
    printf("Enter player name: ");
    scanf(" %[^\n]", newPlayer.name);
    printf("Enter unique kit number (1-99): ");
    scanf("%d", &newPlayer.kitNumber);
    printf("Enter birth date (day month year): ");
    scanf("%d %d %d", &newPlayer.birthDate.day, &newPlayer.birthDate.month, &newPlayer.birthDate.year);
    printf("Enter position: ");
    scanf(" %[^\n]", newPlayer.position);
    strcpy(newPlayer.club, league[teamIndex].teamName);

    if (is_duplicate(league[teamIndex].players, league[teamIndex].activeSize, newPlayer.name, newPlayer.kitNumber)) {
        printf("Duplicate player name or kit number. Player not added.\n");
    } else {
        league[teamIndex].players[league[teamIndex].activeSize] = newPlayer;
        league[teamIndex].activeSize++;
        printf("Player added successfully.\n");
    }
}

void search_update() {
    char searchName[26];
    int kitNumber, choice;
    printf("Search by 1. Name or 2. Kit Number? ");
    scanf("%d", &choice);

    if (choice == 1) {
        printf("Enter player name: ");
        scanf(" %[^\n]", searchName);
    } else if (choice == 2) {
        printf("Enter kit number: ");
        scanf("%d", &kitNumber);
    } else {
        printf("Invalid choice.\n");
        return;
    }

    bool found = false;
    for (int i = 0; i < enrolledTeams; i++) {
        for (int j = 0; j < league[i].activeSize; j++) {

            if ((choice == 1 && strcasecmp(league[i].players[j].name, searchName) == 0) ||
                (choice == 2 && league[i].players[j].kitNumber == kitNumber)) {
                printf("Player found:\n");
                printf("  Name: %s\n  Kit Number: %d\n  Club: %s\n  Birth Date: %02d-%02d-%04d\n  Position: %s\n",
                       league[i].players[j].name, league[i].players[j].kitNumber, league[i].players[j].club,
                       league[i].players[j].birthDate.day, league[i].players[j].birthDate.month, league[i].players[j].birthDate.year,
                       league[i].players[j].position);

                printf("Update player details (leave blank to keep current value):\n");

                printf("Enter new name [%s]: ", league[i].players[j].name);
                char newName[26];
                scanf(" %[^\n]", newName);
                if (strlen(newName) > 0) {
                    strcpy(league[i].players[j].name, newName);
                }

                printf("Enter new kit number [%d]: ", league[i].players[j].kitNumber);
                int newKitNumber;
                if (scanf("%d", &newKitNumber) == 1) {
                    league[i].players[j].kitNumber = newKitNumber;
                }

                printf("Enter new birth date (day month year) [%02d-%02d-%04d]: ", league[i].players[j].birthDate.day, league[i].players[j].birthDate.month, league[i].players[j].birthDate.year);
                int newDay, newMonth, newYear;
                if (scanf("%d %d %d", &newDay, &newMonth, &newYear) == 3) {
                    league[i].players[j].birthDate.day = newDay;
                    league[i].players[j].birthDate.month = newMonth;
                    league[i].players[j].birthDate.year = newYear;
                }

                printf("Enter new position [%s]: ", league[i].players[j].position);
                char newPosition[20];
                scanf(" %[^\n]", newPosition);
                if (strlen(newPosition) > 0) {
                    strcpy(league[i].players[j].position, newPosition);
                }

                printf("Player details updated successfully.\n");
                found = true;
                break;
            }
        }
        if (found) break;
    }

    if (!found) {
        printf("Player not found.\n");
    }
}


void display_club_statistics() {
    for (int i = 0; i < enrolledTeams; i++) {
        printf("Club: %s, Players: %d\n", league[i].teamName, league[i].activeSize);
        int ageSum = 0;
        for (int j = 0; j < league[i].activeSize; j++) {
            int age = 2024 - league[i].players[j].birthDate.year;
            ageSum += age;
            printf("  Name: %s, Kit: %d, Position: %s, Age: %d\n", league[i].players[j].name, league[i].players[j].kitNumber, league[i].players[j].position, age);
        }
        printf("Average Age: %.2f\n", league[i].activeSize ? (float)ageSum / league[i].activeSize : 0.0);
    }
}

int main() {
    int choice;
    do {
        display_menu();
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                enroll_club();
                break;
            case 2:
                add_player();
                break;
            case 3:
                search_update();
                break;
            case 4:
                display_club_statistics();
                break;
            case 0:
                printf("Exiting program.\n");
                break;
            default:
                printf("Invalid choice. Try again.\n");
        }
    } while (choice != 0);

    return 0;
}
