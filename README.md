--Question 1--

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


--Question 2-- 

#include <stdio.h>

#define STARTING_BALANCE 1000
#define MAX_OPS 10

int main() {
    int current_balance = STARTING_BALANCE;
    int operations[MAX_OPS] = {200, -150, -500, -400, -50, -200, 300};
    int pending_ops[MAX_OPS];
    int pending_count = 0;

    printf("Initial balance: %d AED\n", current_balance);

    // Process each transaction
    for (int index = 0; index < MAX_OPS; index++) {
        if (operations[index] == 0) {
            break; 
        }

        int op = operations[index];

        
        if (op < 0 && current_balance + op < 0) {
            printf("Withdrawal of %d AED skipped: Insufficient funds.\n", -op);
            pending_ops[pending_count++] = op;
        } else {
            
            current_balance += op;
            printf("Transaction processed: %d AED, New balance: %d AED\n", op, current_balance);

            if (current_balance == 0) {
                printf("Balance is now zero. Halting further transactions.\n");

                // Mark remaining transactions as pending
                for (int remaining = index + 1; remaining < MAX_OPS; remaining++) {
                    if (operations[remaining] == 0) {
                        break;
                    }
                    pending_ops[pending_count++] = operations[remaining];
                }
                break;
            }
        }
    }

    // Final summary
    printf("\nFinal balance: %d AED\n", current_balance);
    printf("Pending transactions to process later:\n");
    for (int i = 0; i < pending_count; i++) {
        printf("%d ", pending_ops[i]);
    }
    printf("\n");

    return 0;
}

--Question 3--
#include <stdio.h>
#include <string.h>
#include <stdbool.h>

#define MAX_TEAMS 20
#define MAX_PLAYERS 53

typedef struct {
    int day;
    int month;
    int year;
} BirthDate;

typedef struct {
    char name[26];
    int kitNumber;
    char club[30];
    BirthDate birthDate;
    char position[20];
} FootballPlayer;

typedef struct {
    char teamName[21];
    FootballPlayer squad[MAX_PLAYERS];
    int currentSize;
} FootballTeam;

FootballTeam league[MAX_TEAMS];
int totalTeams = 0;

void show_menu() {
    printf("\nMenu:\n");
    printf("1. Register Club\n");
    printf("2. Add New Player\n");
    printf("3. Find and Modify Player\n");
    printf("4. View Club Statistics\n");
    printf("0. Exit\n");
}

void register_club() {
    if (totalTeams >= MAX_TEAMS) {
        printf("Cannot enroll more teams, limit reached.\n");
        return;
    }
    printf("Enter club name: ");
    scanf(" %[^\n]", league[totalTeams].teamName);
    league[totalTeams].currentSize = 0;
    totalTeams++;
    printf("Club registered successfully.\n");
}

bool check_duplicate(FootballPlayer players[], int count, char name[], int kitNumber) {
    for (int i = 0; i < count; i++) {
        if (strcmp(players[i].name, name) == 0 || players[i].kitNumber == kitNumber) {
            return true;
        }
    }
    return false;
}

void add_new_player() {
    if (totalTeams == 0) {
        printf("No clubs available. Please register a club first.\n");
        return;
    }

    printf("Select a club:\n");
    for (int i = 0; i < totalTeams; i++) {
        printf("%d. %s\n", i + 1, league[i].teamName);
    }

    int clubIndex;
    scanf("%d", &clubIndex);
    clubIndex--;

    if (clubIndex < 0 || clubIndex >= totalTeams || league[clubIndex].currentSize >= MAX_PLAYERS) {
        printf("Invalid club or squad full.\n");
        return;
    }

    FootballPlayer newPlayer;
    printf("Enter player's name: ");
    scanf(" %[^\n]", newPlayer.name);
    printf("Enter unique kit number (1-99): ");
    scanf("%d", &newPlayer.kitNumber);
    printf("Enter birth date (day month year): ");
    scanf("%d %d %d", &newPlayer.birthDate.day, &newPlayer.birthDate.month, &newPlayer.birthDate.year);
    printf("Enter position: ");
    scanf(" %[^\n]", newPlayer.position);
    strcpy(newPlayer.club, league[clubIndex].teamName);

    if (check_duplicate(league[clubIndex].squad, league[clubIndex].currentSize, newPlayer.name, newPlayer.kitNumber)) {
        printf("Duplicate player or kit number. Cannot add player.\n");
    } else {
        league[clubIndex].squad[league[clubIndex].currentSize] = newPlayer;
        league[clubIndex].currentSize++;
        printf("Player added successfully.\n");
    }
}

void find_and_modify_player() {
    char playerName[26];
    int playerKitNumber, choice;
    printf("Search by 1. Name or 2. Kit Number? ");
    scanf("%d", &choice);

    if (choice == 1) {
        printf("Enter player's name: ");
        scanf(" %[^\n]", playerName);
    } else if (choice == 2) {
        printf("Enter kit number: ");
        scanf("%d", &playerKitNumber);
    } else {
        printf("Invalid option.\n");
        return;
    }

    bool found = false;
    for (int i = 0; i < totalTeams; i++) {
        for (int j = 0; j < league[i].currentSize; j++) {
            if ((choice == 1 && strcasecmp(league[i].squad[j].name, playerName) == 0) ||
                (choice == 2 && league[i].squad[j].kitNumber == playerKitNumber)) {
                printf("Player found:\n");
                printf("  Name: %s\n  Kit Number: %d\n  Club: %s\n  Birth Date: %02d-%02d-%04d\n  Position: %s\n",
                       league[i].squad[j].name, league[i].squad[j].kitNumber, league[i].squad[j].club,
                       league[i].squad[j].birthDate.day, league[i].squad[j].birthDate.month, league[i].squad[j].birthDate.year,
                       league[i].squad[j].position);

                printf("Modify player details (leave blank to keep current value):\n");

                printf("New name [%s]: ", league[i].squad[j].name);
                char newName[26];
                scanf(" %[^\n]", newName);
                if (strlen(newName) > 0) {
                    strcpy(league[i].squad[j].name, newName);
                }

                printf("New kit number [%d]: ", league[i].squad[j].kitNumber);
                int newKitNumber;
                if (scanf("%d", &newKitNumber) == 1) {
                    league[i].squad[j].kitNumber = newKitNumber;
                }

                printf("New birth date (day month year) [%02d-%02d-%04d]: ", league[i].squad[j].birthDate.day, league[i].squad[j].birthDate.month, league[i].squad[j].birthDate.year);
                int newDay, newMonth, newYear;
                if (scanf("%d %d %d", &newDay, &newMonth, &newYear) == 3) {
                    league[i].squad[j].birthDate.day = newDay;
                    league[i].squad[j].birthDate.month = newMonth;
                    league[i].squad[j].birthDate.year = newYear;
                }

                printf("New position [%s]: ", league[i].squad[j].position);
                char newPosition[20];
                scanf(" %[^\n]", newPosition);
                if (strlen(newPosition) > 0) {
                    strcpy(league[i].squad[j].position, newPosition);
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

void view_club_statistics() {
    for (int i = 0; i < totalTeams; i++) {
        printf("Club: %s, Players: %d\n", league[i].teamName, league[i].currentSize);
        int totalAge = 0;
        for (int j = 0; j < league[i].currentSize; j++) {
            int age = 2024 - league[i].squad[j].birthDate.year;
            totalAge += age;
            printf("  Name: %s, Kit: %d, Position: %s, Age: %d\n", league[i].squad[j].name, league[i].squad[j].kitNumber, league[i].squad[j].position, age);
        }
        printf("Average Age: %.2f\n", league[i].currentSize ? (float)totalAge / league[i].currentSize : 0.0);
    }
}

int main() {
    int choice;
    do {
        show_menu();
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                register_club();
                break;
            case 2:
                add_new_player();
                break;
            case 3:
                find_and_modify_player();
                break;
            case 4:
                view_club_statistics();
                break;
            case 0:
                printf("Exiting program.\n");
                break;
            default:
                printf("Invalid choice. Please try again.\n");
        }
    } while (choice != 0);

    return 0;
}
