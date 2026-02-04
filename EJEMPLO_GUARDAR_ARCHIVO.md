# Ejemplo Completo: Cómo Guardar Datos en Archivo

## Ejemplo 1: Guardar un Empleado en Archivo

### Código de Ejemplo:

```cpp
#include <cstdio>
#include <string>
using namespace std;

class Empleado {
private:
    string nombre;
    string cedula;
    float salarioBruto;

public:
    Empleado(string nombre, string cedula, float salarioBruto) {
        this->nombre = nombre;
        this->cedula = cedula;
        this->salarioBruto = salarioBruto;
    }
    
    // Método para guardar datos en archivo
    void guardarEnArchivo(FILE* archivo) {
        if (archivo) {
            fprintf(archivo, "%s,%s,%.2f", nombre.c_str(), cedula.c_str(), salarioBruto);
        }
    }
};

int main() {
    // Crear un empleado
    Empleado emp("Juan Pérez", "123456789", 500000.0f);
    
    // Abrir archivo en modo escritura ("w" = write)
    FILE* archivo = fopen("empleados.txt", "w");
    
    if (!archivo) {
        printf("Error: No se pudo abrir el archivo\n");
        return 1;
    }
    
    // Guardar el empleado en el archivo
    emp.guardarEnArchivo(archivo);
    
    // Cerrar el archivo
    fclose(archivo);
    
    printf("Datos guardados exitosamente en empleados.txt\n");
    
    return 0;
}
```

### Explicación Paso a Paso:

1. **Abrir el archivo**: `fopen("empleados.txt", "w")`
   - `"w"` = modo escritura (write)
   - Si el archivo existe, se sobrescribe
   - Si no existe, se crea

2. **Verificar que se abrió correctamente**: `if (!archivo)`
   - Si `archivo` es `nullptr`, hubo un error

3. **Escribir datos**: `fprintf(archivo, "%s,%s,%.2f", ...)`
   - `%s` = string (usar `.c_str()` para convertir `string` a `char*`)
   - `%.2f` = float con 2 decimales
   - Los datos se separan con comas

4. **Cerrar el archivo**: `fclose(archivo)`
   - Importante cerrar siempre el archivo

---

## Ejemplo 2: Guardar Múltiples Empleados

```cpp
#include <cstdio>
#include <string>
using namespace std;

class Empleado {
private:
    string nombre;
    string cedula;
    float salarioBruto;

public:
    Empleado(string nombre, string cedula, float salarioBruto) {
        this->nombre = nombre;
        this->cedula = cedula;
        this->salarioBruto = salarioBruto;
    }
    
    void guardarEnArchivo(FILE* archivo) {
        if (archivo) {
            fprintf(archivo, "%s,%s,%.2f\n", nombre.c_str(), cedula.c_str(), salarioBruto);
        }
    }
};

int main() {
    // Crear varios empleados
    Empleado emp1("Juan Pérez", "123456789", 500000.0f);
    Empleado emp2("María González", "987654321", 750000.0f);
    Empleado emp3("Carlos Rodríguez", "456789123", 600000.0f);
    
    // Abrir archivo
    FILE* archivo = fopen("empleados.txt", "w");
    
    if (!archivo) {
        printf("Error: No se pudo abrir el archivo\n");
        return 1;
    }
    
    // Escribir encabezado
    fprintf(archivo, "=== DATOS DE EMPLEADOS ===\n");
    fprintf(archivo, "Nombre,Cédula,SalarioBruto\n");
    
    // Guardar cada empleado
    emp1.guardarEnArchivo(archivo);
    emp2.guardarEnArchivo(archivo);
    emp3.guardarEnArchivo(archivo);
    
    // Cerrar archivo
    fclose(archivo);
    
    printf("Datos guardados exitosamente en empleados.txt\n");
    
    return 0;
}
```

### Contenido del archivo `empleados.txt`:
```
=== DATOS DE EMPLEADOS ===
Nombre,Cédula,SalarioBruto
Juan Pérez,123456789,500000.00
María González,987654321,750000.00
Carlos Rodríguez,456789123,600000.00
```

---

## Ejemplo 3: Guardar desde un Contenedor (Array)

```cpp
#include <cstdio>
#include <string>
using namespace std;

class Empleado {
private:
    string nombre;
    string cedula;
    float salarioBruto;

public:
    Empleado(string nombre, string cedula, float salarioBruto) {
        this->nombre = nombre;
        this->cedula = cedula;
        this->salarioBruto = salarioBruto;
    }
    
    void guardarEnArchivo(FILE* archivo) {
        if (archivo) {
            fprintf(archivo, "%s,%s,%.2f", nombre.c_str(), cedula.c_str(), salarioBruto);
        }
    }
};

class Contenedor {
private:
    Empleado** empleados;
    int cantidad;
    int capacidad;

public:
    Contenedor() {
        cantidad = 0;
        capacidad = 10;
        empleados = new Empleado*[capacidad];
    }
    
    void insertar(Empleado* emp) {
        if (cantidad < capacidad) {
            empleados[cantidad] = emp;
            cantidad++;
        }
    }
    
    void escribir() {
        FILE* archivo = fopen("Empresa.txt", "w");
        
        if (!archivo) {
            printf("Error: No se pudo abrir el archivo\n");
            return;
        }
        
        // Escribir encabezado
        fprintf(archivo, "=== DATOS DE EMPLEADOS ===\n");
        fprintf(archivo, "Nombre,Cédula,SalarioBruto\n");
        
        // Guardar cada empleado
        for (int i = 0; i < cantidad; i++) {
            empleados[i]->guardarEnArchivo(archivo);
            fprintf(archivo, "\n");  // Nueva línea después de cada empleado
        }
        
        fclose(archivo);
        printf("Datos guardados exitosamente en Empresa.txt\n");
    }
};

int main() {
    Contenedor empresa;
    
    // Crear y agregar empleados
    empresa.insertar(new Empleado("Juan Pérez", "123456789", 500000.0f));
    empresa.insertar(new Empleado("María González", "987654321", 750000.0f));
    empresa.insertar(new Empleado("Carlos Rodríguez", "456789123", 600000.0f));
    
    // Guardar todos los empleados en archivo
    empresa.escribir();
    
    return 0;
}
```

---

## Modos de Apertura de Archivo

| Modo | Descripción |
|------|-------------|
| `"w"` | Escritura. Crea el archivo si no existe, lo sobrescribe si existe |
| `"a"` | Append (agregar). Agrega al final del archivo sin borrar lo existente |
| `"r"` | Lectura. Solo lectura, el archivo debe existir |
| `"w+"` | Escritura y lectura. Crea el archivo si no existe |
| `"a+"` | Append y lectura. Agrega al final y permite leer |

---

## Formato de fprintf

```cpp
fprintf(archivo, "Formato", variable1, variable2, ...);
```

### Especificadores de formato comunes:

- `%s` → string (usar `.c_str()`)
- `%d` → int
- `%f` → float
- `%.2f` → float con 2 decimales
- `%c` → char
- `\n` → nueva línea

### Ejemplo:

```cpp
fprintf(archivo, "Nombre: %s, Edad: %d, Salario: %.2f\n", 
        nombre.c_str(), edad, salario);
```

---

## Resumen de Pasos para Guardar en Archivo

1. **Abrir el archivo**: `FILE* archivo = fopen("nombre.txt", "w");`
2. **Verificar**: `if (!archivo) { /* error */ }`
3. **Escribir datos**: `fprintf(archivo, "formato", variables);`
4. **Cerrar el archivo**: `fclose(archivo);`

¡Listo! Ahora puedes guardar datos en archivos.
