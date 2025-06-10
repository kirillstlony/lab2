#include <iostream>
#include <fstream>
#include <ctime>
#include <cstdlib>
#include <chrono>
#include "mkl.h" // Для использования BLAS

using namespace std;
using namespace std::chrono;

// Генерация случайной матрицы
void generateMatrix(float matrix[2048][2048]) {
    for (int i = 0; i < 2048; ++i) {
        for (int j = 0; j < 2048; ++j) {
            matrix[i][j] = static_cast<float>(rand()) / static_cast<float>(RAND_MAX);
        }
    }
}

// Перемножение матриц по формуле из линейной алгебры
void multiplyMatrices(float A[2048][2048], float B[2048][2048], float C[2048][2048]) {
    for (int i = 0; i < 2048; ++i) {
        for (int j = 0; j < 2048; ++j) {
            C[i][j] = 0;
            for (int k = 0; k < 2048; ++k) {
                C[i][j] += A[i][k] * B[k][j];
            }
        }
    }
}

// Перемножение матриц с использованием BLAS
void multiplyMatricesBLAS(float A[2048][2048], float B[2048][2048], float C[2048][2048]) {
    cblas_sgemm(CblasRowMajor, CblasNoTrans, CblasNoTrans,
                2048, 2048, 2048,
                1.0, A, 2048, B, 2048,
                0.0, C, 2048);
}

// Оптимизированный алгоритм перемножения матриц (пример)
void multiplyMatricesOptimized(float A[2048][2048], float B[2048][2048], float C[2048][2048]) {
    // Здесь можно реализовать оптимизированный алгоритм, например, с использованием параллельных вычислений или других методов оптимизации
    // Для примера используем стандартный алгоритм
    multiplyMatrices(A, B, C);
}

int main() {
    float A[2048][2048], B[2048][2048], C[2048][2048];

    generateMatrix(A);
    generateMatrix(B);

    // Первый вариант перемножения
    auto startTime = high_resolution_clock::now();
    multiplyMatrices(A, B, C);
    auto endTime = high_resolution_clock::now();
    auto duration = duration_cast<microseconds>(endTime - startTime);
    double t1 = duration.count() / 1000000.0;
    long long c1 = 2 * pow(2048, 3);
    double p1 = c1 / t1 * 1e-6;

    // Второй вариант перемножения
    startTime = high_resolution_clock::now();
    multiplyMatricesBLAS(A, B, C);
    endTime = high_resolution_clock::now();
    duration = duration_cast<microseconds>(endTime - startTime);
    double t2 = duration.count() / 1000000.0;
    long long c2 = 2 * pow(2048, 3);
    double p2 = c2 / t2 * 1e-6;

    // Третий вариант перемножения
    startTime = high_resolution_clock::now();
    multiplyMatricesOptimized(A, B, C);
    endTime = high_resolution_clock::now();
    duration = duration_cast<microseconds>(endTime - startTime);
    double t3 = duration.count() / 1000000.0;
    long long c3 = 2 * pow(2048, 3);
    double p3 = c3 / t3 * 1e-6;

    cout << "Первый вариант: время = " << t1 << " с, производительность = " << p1 << " MFlops" << endl;
    cout << "Второй вариант: время = " << t2 << " с, производительность = " << p2 << " MFlops" << endl;
    cout << "Третий вариант: время = " << t3 << " с, производительность = " << p3 << " MFlops" << endl;

    return 0;
}
