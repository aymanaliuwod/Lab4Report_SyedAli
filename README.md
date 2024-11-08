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
