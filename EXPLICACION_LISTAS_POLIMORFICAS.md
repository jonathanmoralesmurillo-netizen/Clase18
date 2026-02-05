# Listas Enlazadas Polimórficas - Explicación

## ¿Qué son las Listas Polimórficas?

Una **lista polimórfica** es una lista enlazada que puede almacenar objetos de diferentes tipos, todos relacionados mediante herencia, usando punteros a la clase base. Esto permite tratar diferentes tipos de objetos de manera uniforme gracias al polimorfismo.

---

## Conceptos Clave

### 1. Clase Base Abstracta

```cpp
class Persona {
public:
    virtual string toString() = 0;  // Método virtual puro
};
```

- `Persona` es una clase **abstracta** porque tiene un método virtual puro
- No se puede instanciar directamente: `Persona p;` ❌
- Solo se puede usar como puntero: `Persona* p;` ✅

### 2. Polimorfismo en la Lista

```cpp
class Nodo {
private:
    Persona* persona;  // Puntero polimórfico
    Nodo* siguiente;
};
```

- El nodo almacena un `Persona*` (puntero a la clase base)
- Puede apuntar a objetos `Estudiante` o `Profesor`
- Gracias al polimorfismo, cada objeto ejecuta su propio `toString()`

### 3. Enlace Dinámico (Dynamic Binding)

Cuando llamamos `persona->toString()`:
- El compilador verifica que `toString()` es virtual
- En tiempo de ejecución, se determina el tipo real del objeto
- Se ejecuta el método correspondiente al tipo real

**Ejemplo:**
```cpp
Persona* p1 = new Estudiante(...);
Persona* p2 = new Profesor(...);

p1->toString();  // Ejecuta Estudiante::toString()
p2->toString();  // Ejecuta Profesor::toString()
```

---

## Estructura de la Lista Polimórfica

```
Lista
  │
  └─── Nodo* primero
       │
       ├─── Persona* persona → Estudiante
       └─── Nodo* siguiente
            │
            ├─── Persona* persona → Profesor
            └─── Nodo* siguiente
                 │
                 └─── ...
```

---

## Ventajas de las Listas Polimórficas

1. **Flexibilidad**: Puede almacenar diferentes tipos de objetos relacionados
2. **Código uniforme**: Se puede tratar todos los objetos igual usando `Persona*`
3. **Extensibilidad**: Fácil agregar nuevos tipos sin modificar la lista
4. **Polimorfismo**: Cada objeto ejecuta su propia versión de los métodos virtuales

---

## Uso de dynamic_cast

Cuando necesitamos acceder a métodos específicos de una subclase:

```cpp
// Verificar si es Estudiante
Estudiante* pE = dynamic_cast<Estudiante*>(persona);
if (pE != nullptr) {
    pE->incrementarPromedio();  // Método específico de Estudiante
}

// Verificar si es Profesor
Profesor* pP = dynamic_cast<Profesor*>(persona);
if (pP != nullptr) {
    pP->incrementarSalario();  // Método específico de Profesor
}
```

**¿Por qué usar dynamic_cast?**
- `persona->incrementarPromedio()` ❌ No funciona (método no existe en Persona)
- Necesitamos convertir a `Estudiante*` para acceder a métodos específicos
- `dynamic_cast` devuelve `nullptr` si la conversión falla (seguro)

---

## Ejemplo de Recorrido Polimórfico

```cpp
void Lista::toString() {
    actual = primero;
    while (actual != nullptr) {
        // Polimorfismo: cada objeto ejecuta su propio toString()
        cout << actual->getPersona()->toString();
        actual = actual->getSiguiente();
    }
}
```

**Ejecución:**
- Si `persona` apunta a un `Estudiante` → ejecuta `Estudiante::toString()`
- Si `persona` apunta a un `Profesor` → ejecuta `Profesor::toString()`

---

## Resumen

**Listas Polimórficas permiten:**
- ✅ Almacenar diferentes tipos de objetos relacionados por herencia
- ✅ Tratar todos los objetos de manera uniforme mediante la clase base
- ✅ Ejecutar métodos específicos de cada tipo usando polimorfismo
- ✅ Acceder a métodos específicos usando `dynamic_cast`
