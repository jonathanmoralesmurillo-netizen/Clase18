# Clase 18 - Resolución Ejercicio de Examen: Sistema de Empleados

## Enunciado del Problema

**Ejercicio de Examen Pasado**

**Problema Nº4 (valor 32 pts)**

En una empresa, se encuentra un conjunto de empleados. Cada uno de ellos tiene como características las siguientes: nombre (string), cédula(string), salarioBruto (float) y se requiere que usted realice una serie de cálculos con la información de estos empleados. Para ello es necesario un contenedor que pueda crecer y disminuir de manera indefinida.

**Puede asumir que la clase empleado ya existe y cuenta con todos sus constructores, métodos set y get, así como el método toString() ya implementados.**

Según el problema anterior, realice lo siguiente:

### Requerimientos:

1. **Realice un método en la clase Empleado que permita guardar sus datos en un archivo de texto** (valor 2pts) (archivos n)

2. **En el contenedor implemente:**
   - Un método llamado `float TotalSalarioNetos()` (valor 10pts) que permita calcular el monto total de salariosNetos que debe pagar la empresa.
     - Para ello debe considerar que el salario Neto se calcula como: Salario Bruto menos las deducciones de un empleado. En este caso, debe descontarle un 25% a cada empleado por concepto de la ley del Seguro Social.
   - Un método llamado `string toString()` que permita mostrar únicamente la cedula de la persona, y su correspondiente salarioNeto (Valor 5pts)
   - Un método llamado `void escribir()` que le permita al contenedor almacenar a todos los datos en el archivo Empresa.txt (valor 10pts)

3. **En main(), para usar el contenedor:**
   - (1pt). Cree una instancia estática del contenedor.
   - (1pt). Cree tres objetos dinámicos de la clase Empleado con el constructor con parámetro.
   - (1pt). Inserte los datos en el contenedor, y muéstrelos.
   - (1pt). Calcule el monto total a pagar por parte de la empresa
   - (1pt). Llame al método escribir para que permita almacenar los datos en el archivo

---

## Tipos de Datos

Los nombres de variables y métodos se respetan exactamente como aparecen en el enunciado:

- **nombre** → `string nombre` ✓
- **cedula** → `string cedula` ✓
- **salarioBruto** → `float salarioBruto` ✓
- **TotalSalarioNetos()** → `float TotalSalarioNetos()` ✓
- **toString()** → `string toString()` ✓
- **escribir()** → `void escribir()` ✓

**Nota:** Se utiliza `using namespace std;` para simplificar el código.

---

## Solución Paso a Paso

### Paso 1: Clase Empleado

```cpp
#ifndef EMPLEADO_H
#define EMPLEADO_H

#include <string>
#include <cstdio>  // Para FILE*
using namespace std;

class Empleado {
private:
    string nombre;
    string cedula;
    float salarioBruto;

public:
    // Constructor por defecto
    Empleado();
    
    // Constructor con parámetros
    Empleado(const string& nombre, const string& cedula, float salarioBruto);
    
    // Destructor
    ~Empleado();
    
    // Getters
    string getNombre() { return nombre; }
    string getCedula() { return cedula; }
    float getSalarioBruto() { return salarioBruto; }
    
    // Setters
    void setNombre(const string& nombre);
    void setCedula(const string& cedula);
    void setSalarioBruto(float salarioBruto);
    
    // Método para calcular salario neto
    float calcularSalarioNeto();
    
    // Método toString() existente (asumido)
    string toString();
    
    // REQUERIMIENTO 1: Método para guardar datos en archivo (2 pts)
    void guardarEnArchivo(FILE* archivo);
};

#endif // EMPLEADO_H
```

#### Implementación de Empleado:

```cpp
#include "Empleado.h"
#include <cstdio>
#include <sstream>
using namespace std;

Empleado::Empleado() {
    nombre = "";
    cedula = "";
    salarioBruto = 0.0f;
}

Empleado::Empleado(const string& nombre, const string& cedula, float salarioBruto) {
    this->nombre = nombre;
    this->cedula = cedula;
    this->salarioBruto = salarioBruto;
}

Empleado::~Empleado() {
    // No necesita liberar memoria, string lo hace automáticamente
}

void Empleado::setNombre(const string& nombre) {
    this->nombre = nombre;
}

void Empleado::setCedula(const string& cedula) {
    this->cedula = cedula;
}

void Empleado::setSalarioBruto(float salarioBruto) {
    this->salarioBruto = salarioBruto;
}

float Empleado::calcularSalarioNeto() {
    return salarioBruto * 0.75f;  // Descuento del 25% (queda 75%)
}

string Empleado::toString() {
    ostringstream oss;
    oss << "Nombre: " << nombre << ", Cédula: " << cedula 
        << ", Salario Bruto: $" << salarioBruto;
    return oss.str();
}

// REQUERIMIENTO 1: Guardar datos en archivo (2 pts)
void Empleado::guardarEnArchivo(FILE* archivo) {
    if (archivo) {
        fprintf(archivo, "%s,%s,%.2f", nombre.c_str(), cedula.c_str(), salarioBruto);
    }
}
```

---

### Paso 2: Clase Contenedor

```cpp
#ifndef CONTENEDOR_H
#define CONTENEDOR_H

#include "Empleado.h"
#include <string>
using namespace std;

class Contenedor {
private:
    Empleado** empleados;      // Array dinámico de punteros a Empleado
    int cantidad;              // Cantidad actual de empleados
    int capacidad;             // Capacidad del array
    
    // Método auxiliar para redimensionar
    void redimensionar();

public:
    // Constructor
    Contenedor();
    
    // Destructor
    ~Contenedor();
    
    // Constructor de copia (deshabilitado)
    Contenedor(const Contenedor&) = delete;
    Contenedor& operator=(const Contenedor&) = delete;
    
    // Método para insertar empleado
    void insertar(Empleado* empleado);
    
    // REQUERIMIENTO 2a: Calcular total de salarios netos (10 pts)
    float TotalSalarioNetos();
    
    // REQUERIMIENTO 2b: toString() del contenedor (5 pts)
    string toString();
    
    // REQUERIMIENTO 2c: Escribir todos los datos en archivo (10 pts)
    void escribir();
    
    // Getter para cantidad
    int getCantidad() { return cantidad; }
};

#endif // CONTENEDOR_H
```

#### Implementación del Contenedor:

```cpp
#include "Contenedor.h"
#include <cstdio>
#include <sstream>
using namespace std;

Contenedor::Contenedor() {
    cantidad = 0;
    capacidad = 10;
    empleados = new Empleado*[capacidad];
    for (int i = 0; i < capacidad; i++) {
        empleados[i] = nullptr;
    }
}

Contenedor::~Contenedor() {
    // Eliminar todos los empleados
    for (int i = 0; i < cantidad; i++) {
        delete empleados[i];
    }
    delete[] empleados;
}

void Contenedor::redimensionar() {
    int nuevaCapacidad = capacidad * 2;
    Empleado** nuevoArray = new Empleado*[nuevaCapacidad];
    
    // Copiar empleados existentes
    for (int i = 0; i < cantidad; i++) {
        nuevoArray[i] = empleados[i];
    }
    
    // Inicializar el resto
    for (int i = cantidad; i < nuevaCapacidad; i++) {
        nuevoArray[i] = nullptr;
    }
    
    delete[] empleados;
    empleados = nuevoArray;
    capacidad = nuevaCapacidad;
}

void Contenedor::insertar(Empleado* empleado) {
    if (cantidad >= capacidad) {
        redimensionar();
    }
    empleados[cantidad] = empleado;
    cantidad++;
}

// REQUERIMIENTO 2a: Calcular total de salarios netos (10 pts)
float Contenedor::TotalSalarioNetos() {
    float total = 0.0f;
    
    for (int i = 0; i < cantidad; i++) {
        // Calcular salario neto de cada empleado
        // Salario Neto = Salario Bruto - 25% (Seguro Social)
        // Salario Neto = Salario Bruto * 0.75
        float salarioNeto = empleados[i]->getSalarioBruto() * 0.75f;
        total += salarioNeto;
    }
    
    return total;
}

// REQUERIMIENTO 2b: toString() del contenedor (5 pts)
string Contenedor::toString() {
    ostringstream oss;
    
    for (int i = 0; i < cantidad; i++) {
        // Mostrar únicamente cédula y salario neto
        float salarioNeto = empleados[i]->getSalarioBruto() * 0.75f;
        oss << "Cédula: " << empleados[i]->getCedula() 
            << ", Salario Neto: $" << salarioNeto << endl;
    }
    
    return oss.str();
}

// REQUERIMIENTO 2c: Escribir todos los datos en archivo (10 pts)
void Contenedor::escribir() {
    FILE* archivo = fopen("Empresa.txt", "w");
    
    if (!archivo) {
        printf("Error: No se pudo abrir el archivo Empresa.txt\n");
        return;
    }
    
    // Escribir encabezado
    fprintf(archivo, "=== DATOS DE EMPLEADOS ===\n");
    fprintf(archivo, "Nombre,Cédula,SalarioBruto,SalarioNeto\n");
    
    // Escribir datos de cada empleado
    for (int i = 0; i < cantidad; i++) {
        // Usar el método guardarEnArchivo de cada empleado
        empleados[i]->guardarEnArchivo(archivo);
        
        // Escribir el salario neto calculado
        float salarioNeto = empleados[i]->getSalarioBruto() * 0.75f;
        fprintf(archivo, ",%.2f\n", salarioNeto);
    }
    
    fclose(archivo);
    printf("Datos guardados exitosamente en Empresa.txt\n");
}
```

---

### Paso 3: Implementación del main()

```cpp
#include <cstdio>
#include <iostream>
#include "Contenedor.h"
#include "Empleado.h"
using namespace std;

int main() {
    // REQUERIMIENTO 3a: Crear instancia estática del contenedor (1 pt)
    Contenedor empresa;
    
    // REQUERIMIENTO 3b: Crear tres objetos dinámicos de Empleado (1 pt)
    Empleado* emp1 = new Empleado("Juan Pérez", "123456789", 500000.0f);
    Empleado* emp2 = new Empleado("María González", "987654321", 750000.0f);
    Empleado* emp3 = new Empleado("Carlos Rodríguez", "456789123", 600000.0f);
    
    // REQUERIMIENTO 3c: Insertar datos en el contenedor y mostrarlos (1 pt)
    empresa.insertar(emp1);
    empresa.insertar(emp2);
    empresa.insertar(emp3);
    
    printf("=== EMPLEADOS EN LA EMPRESA ===\n");
    string resultado = empresa.toString();
    printf("%s", resultado.c_str());
    
    // REQUERIMIENTO 3d: Calcular el monto total a pagar (1 pt)
    float totalNeto = empresa.TotalSalarioNetos();
    printf("\n=== TOTAL A PAGAR ===\n");
    printf("Total de Salarios Netos: $%.2f\n", totalNeto);
    
    // REQUERIMIENTO 3e: Llamar al método escribir (1 pt)
    empresa.escribir();
    
    // Nota: No eliminamos los empleados aquí porque el destructor del contenedor
    // se encarga de liberar la memoria automáticamente
    
    return 0;
}
```

---

## Ejemplo de Salida

```
=== EMPLEADOS EN LA EMPRESA ===
Cédula: 123456789, Salario Neto: $375000.00
Cédula: 987654321, Salario Neto: $562500.00
Cédula: 456789123, Salario Neto: $450000.00

=== TOTAL A PAGAR ===
Total de Salarios Netos: $1387500.00
Datos guardados exitosamente en Empresa.txt
```

---

## Archivo Empresa.txt Generado

```
=== DATOS DE EMPLEADOS ===
Nombre,Cédula,SalarioBruto,SalarioNeto
Juan Pérez,123456789,500000.00,375000.00
María González,987654321,750000.00,562500.00
Carlos Rodríguez,456789123,600000.00,450000.00
```

---

## Compilación

```bash
g++ -std=c++11 -Wall -Wextra Empleado.cpp Contenedor.cpp main.cpp -o empresa
```

---

## Resumen de Requerimientos Cumplidos

| Requerimiento | Puntos | Estado |
|---------------|--------|--------|
| Método `guardarEnArchivo()` en Empleado | 2 pts | ✅ |
| Método `TotalSalarioNetos()` | 10 pts | ✅ |
| Método `toString()` del Contenedor | 5 pts | ✅ |
| Método `escribir()` | 10 pts | ✅ |
| Instancia estática del contenedor | 1 pt | ✅ |
| Crear objetos dinámicos | 1 pt | ✅ |
| Insertar y mostrar | 1 pt | ✅ |
| Calcular total | 1 pt | ✅ |
| Llamar método escribir | 1 pt | ✅ |
| **TOTAL** | **32 pts** | ✅ |

