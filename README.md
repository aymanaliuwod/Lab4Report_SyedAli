-- Question 1 --
#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>

// Function to check if a string can be converted to a float
int is_valid_float(const char *str) {
    char *endptr;
    strtod(str, &endptr); // Convert string to double
    return (*endptr == '\0' || isspace(*endptr)); // Valid if endptr points to null or whitespace
}

int main() {
    FILE *inputFile, *outputFile;
    char inputFileName[] = "data.txt";  // Input file name
    char outputFileName[] = "valid_data.txt"; // Output file name
    char buffer[100];
    int invalidCount = 0;

    // Open the input file
    inputFile = fopen(inputFileName, "r");
    if (inputFile == NULL) {
        perror("Error opening source file");
        return EXIT_FAILURE;
    }

    // Open the output file
    outputFile = fopen(outputFileName, "w");
    if (outputFile == NULL) {
        perror("Error opening output file");
        fclose(inputFile);
        return EXIT_FAILURE;
    }

    // Use fseek to check file position and move the pointer
    fseek(inputFile, 0, SEEK_SET);  // Move the file pointer to the beginning of the file
    long startPos = ftell(inputFile);  // Store the starting position of the file pointer

    // Process the file
    while (fscanf(inputFile, "%s", buffer) != EOF) {
        if (is_valid_float(buffer)) {
            fprintf(outputFile, "%s\n", buffer); // Write valid float to output file
        } else {
            invalidCount++; // Increment invalid count
        }
    }

    // Check for errors in input file
    if (ferror(inputFile)) {
        perror("Error reading from source file");
        fclose(inputFile);
        fclose(outputFile);
        return EXIT_FAILURE;
    }

    // Use fseek to get the current position after reading
    fseek(inputFile, 0, SEEK_END);  // Move the pointer to the end of the file
    long endPos = ftell(inputFile);  // Get the current position at the end of the file
    printf("File size: %ld bytes\n", endPos - startPos);

    // Close files
    fclose(inputFile);
    fclose(outputFile);

    // Display results
    printf("Number of invalid values found: %d\n", invalidCount);
    printf("Valid floats have been written to '%s'\n", outputFileName);

    return EXIT_SUCCESS;
}

-- Question 2--

#include <stdio.h>
#include <stdlib.h>

#define WIDTH 512
#define HEIGHT 512

// Function to read a PGM image in text format.
int readPGMText(const char *filename, unsigned char *pixels, int width, int height) {
    FILE *file = fopen(filename, "r");
    if (!file) {
        printf("Error: Cannot open file %s\n", filename);
        return -1;
    }

    char buffer[16];
    fgets(buffer, sizeof(buffer), file);  // Read magic number (P2)

    // Skip comments
    do {
        fgets(buffer, sizeof(buffer), file);
    } while (buffer[0] == '#');

    // Read width, height, and max gray value
    int imgWidth, imgHeight, maxVal;
    sscanf(buffer, "%d %d", &imgWidth, &imgHeight);
    fscanf(file, "%d", &maxVal);

    // Ensure dimensions match
    if (imgWidth != width || imgHeight != height) {
        printf("Error: Image dimensions do not match.\n");
        fclose(file);
        return -1;
    }

    // Read pixel values
    for (int i = 0; i < width * height; i++) {
        fscanf(file, "%hhu", &pixels[i]);
    }

    fclose(file);
    return 0;
}

// Function to write a PGM image in text format.
int writePGMText(const char *filename, unsigned char *pixels, int width, int height) {
    FILE *file = fopen(filename, "w");
    if (!file) {
        printf("Error: Cannot open file %s\n", filename);
        return -1;
    }

    fprintf(file, "P2\n");
    fprintf(file, "# Created by Steganography Program\n");
    fprintf(file, "%d %d\n", width, height);
    fprintf(file, "255\n");

    for (int i = 0; i < width * height; i++) {
        fprintf(file, "%d ", pixels[i]);
    }

    fclose(file);
    return 0;
}

// Function to write a PGM image in binary format.
int writePGMBinary(const char *filename, unsigned char *pixels, int width, int height) {
    FILE *file = fopen(filename, "wb");
    if (!file) {
        printf("Error: Cannot open file %s\n", filename);
        return -1;
    }

    fprintf(file, "P5\n");
    fprintf(file, "# Created by Steganography Program\n");
    fprintf(file, "%d %d\n", width, height);
    fprintf(file, "255\n");

    fwrite(pixels, sizeof(unsigned char), width * height, file);

    fclose(file);
    return 0;
}

// Function to hide a secret image using the 4-bit LSB steganography algorithm.
void embedLSB(unsigned char *coverPixels, unsigned char *secretPixels, int width, int height) {
    for (int i = 0; i < width * height; i++) {
        unsigned char coverMSB = coverPixels[i] & 0xF0;  // Upper 4 bits of cover image
        unsigned char secretMSB = (secretPixels[i] & 0xF0) >> 4;  // Upper 4 bits of secret image

        coverPixels[i] = coverMSB | secretMSB;  // Combine cover and secret image bits
    }
}

// Function to extract the secret image using 4-bit LSB steganography algorithm.
void extractLSB(unsigned char *coverPixels, unsigned char *outputPixels, int width, int height) {
    for (int i = 0; i < width * height; i++) {
        unsigned char coverMSB = coverPixels[i] & 0xF0;  // Upper 4 bits of cover image
        unsigned char secretMSB = (coverPixels[i] & 0x0F) << 4;  // Lower 4 bits from stego image

        coverPixels[i] = coverMSB;       // Reconstructed Cover Image
        outputPixels[i] = secretMSB;     // Extracted Secret Image
    }
}

int main() {
    // Relative file paths since the code, cover image, and secret image are in the same folder
    char cover_image[] = "baboon.pgm";
    char secret_image[] = "farm.pgm";
    char stego_image[] = "stego_image_bin.pgm";
    char extracted_secret[] = "extracted_secret.pgm";

    unsigned char *coverPixels, *secretPixels, *outputPixels;
    int coverWidth = WIDTH, coverHeight = HEIGHT, secretWidth = WIDTH, secretHeight = HEIGHT;

    // Allocate memory for cover, secret, and output image pixels
    coverPixels = (unsigned char *)malloc(WIDTH * HEIGHT * sizeof(unsigned char));
    secretPixels = (unsigned char *)malloc(WIDTH * HEIGHT * sizeof(unsigned char));
    outputPixels = (unsigned char *)malloc(WIDTH * HEIGHT * sizeof(unsigned char));

    if (!coverPixels || !secretPixels || !outputPixels) {
        printf("Error: Memory allocation failed.\n");
        return -1;
    }

    // Read the cover and secret image files
    if (readPGMText(cover_image, coverPixels, coverWidth, coverHeight) == -1) {
        printf("Error: Cannot read cover image file.\n");
        return -1;
    }

    if (readPGMText(secret_image, secretPixels, secretWidth, secretHeight) == -1) {
        printf("Error: Cannot read secret image file.\n");
        return -1;
    }

    // Check if the dimensions of both images match
    if (coverWidth != secretWidth || coverHeight != secretHeight) {
        printf("Error: Image dimensions do not match.\n");
        return -1;
    }

    // Embed the secret image into the cover image
    embedLSB(coverPixels, secretPixels, coverWidth, coverHeight);

    // Save the stego image in binary format
    if (writePGMBinary(stego_image, coverPixels, coverWidth, coverHeight) == -1) {
        printf("Error: Cannot write stego image.\n");
        return -1;
    }

    // Extract the secret image from the stego image
    extractLSB(coverPixels, outputPixels, coverWidth, coverHeight);

    // Save the extracted secret image in text format
    if (writePGMText(extracted_secret, outputPixels, coverWidth, coverHeight) == -1) {
        printf("Error: Cannot write extracted secret image.\n");
        return -1;
    }

    // Free allocated memory
    free(coverPixels);
    free(secretPixels);
    free(outputPixels);

    printf("Steganography completed successfully.\n");
    return 0;
}

--Question 3--
#include <iostream>
#include <iomanip>
#include <cstdlib> 
using namespace std;

// Function to print real numbers in fixed-point notation
void print_real(float number, int fieldspace, int precision);

// Template function to update the scale of two variables
template <typename T>
void update_scale(T& m1, T& m2, T m3 = 10) {
    // Update m1 with the sum of m1 and m2, then multiply by m3
    m1 = (m1 + m2) * m3;

    // Update m2 with the difference of m1 and m2, then multiply by m3
    m2 = (m1 - m2) * m3;
}

int main(void) {
    float a, b;
    cout << "Please input two real numbers: ";
    cin >> a >> b;

    // Print the values of a and b with 3 decimal places
    print_real(a, 7, 3);
    print_real(b, 7, 3);

    // Call the function update_scale with the actual parameters a and b
    update_scale(a, b);

    // Print the updated values of a and b
    print_real(a, 7, 3);
    print_real(b, 7, 3);

    return 0;
}

// Function to print real numbers in fixed-point notation with specified field width and precision
void print_real(float number, int fieldspace, int precision) {
    cout << fixed << setw(fieldspace) << setprecision(precision) << number << endl;
}
