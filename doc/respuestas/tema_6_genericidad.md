# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

### Respuesta

Una forma básica de almacenar valores de distintos tipos consiste en usar un array cuyo tipo sea lo suficientemente general. En C se podría usar `void*`, porque un puntero `void*` puede apuntar a datos de cualquier tipo. En Java se puede usar `Object`, porque todas las clases heredan directa o indirectamente de `Object`.

Por ejemplo, en Java se podría construir una bolsa sencilla que internamente use un array de `Object`. Esta estructura permite guardar cadenas, enteros, objetos propios, etc. El problema, como se verá después, es que al recuperar los valores se pierde información concreta de tipo y puede hacer falta convertir manualmente.

```java
public class BolsaObject {
    private Object[] elementos;
    private int cantidad;

    public BolsaObject(int capacidad) {
        elementos = new Object[capacidad];
        cantidad = 0;
    }

    public void agregar(Object valor) {
        elementos[cantidad] = valor;
        cantidad++;
    }

    public Object obtener(int indice) {
        return elementos[indice];
    }
}

BolsaObject bolsa = new BolsaObject(3);
bolsa.agregar("Hola");
bolsa.agregar(42);
bolsa.agregar(3.14);

String texto = (String) bolsa.obtener(0);
Integer numero = (Integer) bolsa.obtener(1);
```

En C la idea sería parecida usando `void*`, aunque se debe gestionar manualmente la memoria y recordar el tipo real de cada dato almacenado. El compilador no puede saber qué hay detrás de cada puntero, por lo que el programador asume más responsabilidad.

```c
#include <stdio.h>

#define CAPACIDAD 3

int main() {
    void* elementos[CAPACIDAD];

    int entero = 42;
    double real = 3.14;
    char* texto = "Hola";

    elementos[0] = &entero;
    elementos[1] = &real;
    elementos[2] = texto;

    printf("%d\n", *(int*) elementos[0]);
    printf("%f\n", *(double*) elementos[1]);
    printf("%s\n", (char*) elementos[2]);

    return 0;
}
```

## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica? 

### Respuesta

La programación genérica consiste en escribir código que pueda trabajar con distintos tipos de datos sin duplicar la misma lógica para cada tipo concreto. La idea importante no es “guardar cualquier cosa porque sí”, sino construir algoritmos o estructuras reutilizables manteniendo, cuando sea posible, seguridad de tipos.

Por ejemplo, una lista dinámica no debería tener que reescribirse como `ListaDeEnteros`, `ListaDeStrings`, `ListaDePersonas`, etc. Conceptualmente, todas esas listas tienen las mismas operaciones: agregar, obtener, eliminar, recorrer. Lo que cambia es el tipo de dato almacenado.

El ejemplo anterior con `Object` o `void*` puede considerarse una forma muy básica y primitiva de programación genérica, porque permite reutilizar una estructura con distintos tipos. Sin embargo, es una solución débil: sacrifica información de tipo y desplaza errores que podrían detectarse en compilación hacia el tiempo de ejecución.

## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas. 

### Respuesta

El principal problema es que el compilador deja de poder comprobar correctamente qué tipo concreto se está usando. Si una estructura almacena `Object`, Java solo sabe que cada elemento es un `Object`, aunque en realidad haya dentro un `String`, un `Integer` o una `Persona`. Por eso, al recuperar un elemento, suele ser necesario hacer un downcasting.

Ese downcasting puede fallar en tiempo de ejecución. Por ejemplo, si se guarda un `Integer` y luego se intenta recuperarlo como `String`, el código compila, pero al ejecutarse produce una `ClassCastException`. Es un error bastante peligroso porque no se detecta donde se inserta el dato, sino más tarde, cuando se usa.

```java
BolsaObject bolsa = new BolsaObject(1);
bolsa.agregar(42);

String texto = (String) bolsa.obtener(0); // Compila, pero falla en ejecución
```

En C, con `void*`, el problema es incluso más delicado. El compilador tampoco sabe el tipo real apuntado y un cast incorrecto puede provocar lecturas inválidas de memoria, resultados absurdos o errores difíciles de depurar. Ahí no hay una excepción segura como en Java: se está mucho más cerca del comportamiento indefinido.

## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**? 

### Respuesta

Los parámetros de tipo son nombres simbólicos que representan tipos que todavía no se han concretado. Se usan al definir una clase, interfaz o método genérico, y luego se sustituyen conceptualmente por un tipo real cuando se usa esa clase o método. En Java suelen escribirse como `T`, `E`, `K`, `V`, aunque podrían tener otros nombres.

Por ejemplo, en `List<String>`, `String` es el tipo concreto usado para el parámetro de tipo de `List`. Gracias a eso, el compilador sabe que esa lista contiene cadenas, impide insertar valores incompatibles y permite recuperar elementos como `String` sin hacer casting manual.

```java
List<String> nombres = new ArrayList<>();
nombres.add("Ana");
// nombres.add(42); // Error de compilación

String nombre = nombres.get(0); // No hace falta casting
```

La ventaja clave es que se obtiene reutilización sin renunciar al chequeo estático de tipos. Es decir, se escribe una sola clase `List<E>`, pero se puede usar como lista de cadenas, de enteros, de personas o de cualquier otro tipo permitido.

## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

### Respuesta

En Java, la programación genérica se usa mediante generics. Si se declara una `ArrayList<String>`, el compilador impide insertar elementos que no sean `String` y permite recorrer la lista sabiendo que cada elemento recuperado es una cadena.

```java
import java.util.ArrayList;
import java.util.List;

public class EjemploJava {
    public static void main(String[] args) {
        List<String> palabras = new ArrayList<>();
        palabras.add("uno");
        palabras.add("dos");
        palabras.add("tres");

        for (String palabra : palabras) {
            System.out.println(palabra.toUpperCase());
        }
    }
}
```

En C++, la idea equivalente puede verse con `std::vector<std::string>`. El vector queda especializado para almacenar objetos `std::string`, por lo que intentar insertar un tipo incompatible produce un error de compilación.

```cpp
#include <iostream>
#include <string>
#include <vector>

int main() {
    std::vector<std::string> palabras;
    palabras.push_back("uno");
    palabras.push_back("dos");
    palabras.push_back("tres");

    for (const std::string& palabra : palabras) {
        std::cout << palabra << std::endl;
    }

    return 0;
}
```

En ambos casos se expresa la misma intención: la estructura es genérica en su definición, pero en este uso concreto solo admite cadenas. La diferencia importante no está en el uso superficial, sino en cómo cada compilador implementa internamente esa genericidad.

## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

### Respuesta

Java y C++ no implementan la programación genérica de la misma forma. Aunque en ambos lenguajes se escriba algo parecido a “lista de `String`”, el compilador toma caminos diferentes. Esta diferencia es fundamental, porque afecta al código generado, a la información de tipos disponible en ejecución y a ciertos límites del lenguaje.

En Java se usa un mecanismo llamado `type erasure`, o borrado de tipos. El compilador comprueba los tipos genéricos durante la compilación, inserta casts donde sean necesarios y después elimina gran parte de la información genérica del bytecode. Por eso, en tiempo de ejecución, una `List<String>` y una `List<Integer>` son básicamente listas del mismo tipo bruto (`List`).

```java
List<String> textos = new ArrayList<>();
List<Integer> numeros = new ArrayList<>();

System.out.println(textos.getClass() == numeros.getClass()); // true
```

En C++, en cambio, los templates se instancian generando código específico para cada combinación de tipos usada. Un `std::vector<std::string>` y un `std::vector<int>` dan lugar a especializaciones distintas. Esto permite más optimización y conserva más información en el código generado, pero también puede aumentar el tamaño del binario si hay muchas instanciaciones.

## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`. 

### Respuesta

Una clase genérica puede tener más de un parámetro de tipo. En este caso, `Par<A, B>` representa una pareja de valores donde el primer valor tiene tipo `A` y el segundo valor tiene tipo `B`. No es necesario que ambos tipos sean iguales.

```java
public class Par<A, B> {
    private final A primero;
    private final B segundo;

    public Par(A primero, B segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public A getPrimero() {
        return primero;
    }

    public B getSegundo() {
        return segundo;
    }
}
```

Se puede usar para devolver dos resultados relacionados desde un método. Por ejemplo, una función estadística puede devolver la media y la desviación típica de un array de `double`. En este caso ambos valores son `Double`, pero la clase permitiría que fueran tipos distintos si hiciera falta.

```java
public static Par<Double, Double> calcularMediaYDesviacion(double[] datos) {
    double suma = 0.0;
    for (double dato : datos) {
        suma += dato;
    }
    double media = suma / datos.length;

    double sumaCuadrados = 0.0;
    for (double dato : datos) {
        sumaCuadrados += Math.pow(dato - media, 2);
    }
    double desviacion = Math.sqrt(sumaCuadrados / datos.length);

    return new Par<>(media, desviacion);
}

Par<Double, Double> resultado = calcularMediaYDesviacion(new double[] {2.0, 4.0, 6.0});
System.out.println("Media: " + resultado.getPrimero());
System.out.println("Desviación: " + resultado.getSegundo());
```

La ventaja es que el tipo de retorno comunica claramente qué contiene el resultado. El compilador sabe que `getPrimero()` y `getSegundo()` devuelven `Double`, sin obligar a hacer conversiones manuales.

## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

### Respuesta

Un método genérico declara sus propios parámetros de tipo antes del tipo de retorno. En este caso, `<T>` indica que el método trabaja con un tipo `T`, que será inferido por el compilador según los argumentos usados en cada llamada.

```java
import java.util.Random;

public static <T> T seleccionaUno(T primero, T segundo) {
    Random random = new Random();
    return random.nextBoolean() ? primero : segundo;
}

String elegido = seleccionaUno("rojo", "azul");
Integer numero = seleccionaUno(10, 20);
```

Si se hiciera con `Object`, el método sería más débil. Al devolver `Object`, quien llama tendría que convertir manualmente el resultado al tipo esperado. Además, el método aceptaría mezclar tipos sin que el compilador lo impidiera claramente.

```java
public static Object seleccionaUnoObject(Object primero, Object segundo) {
    return Math.random() < 0.5 ? primero : segundo;
}

String texto = (String) seleccionaUnoObject("hola", "adiós");
Object raro = seleccionaUnoObject("hola", 42); // Compila, aunque mezcla tipos
```

Con el método genérico, el resultado se obtiene ya con el tipo correcto y se evita el downcasting. Además, se expresa que los dos argumentos deben pertenecer al mismo tipo `T`. Java puede inferir un supertipo común si se mezclan tipos, pero el diseño genérico sigue siendo más seguro y comunicativo que trabajar directamente con `Object`.

## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

### Respuesta

Sí, Java permite restringir parámetros de tipo mediante cotas. Por ejemplo, `<T extends Number>` significa que `T` debe ser `Number` o una subclase de `Number`, como `Integer`, `Double`, `Long`, etc. Esto permite usar métodos definidos en `Number`, como `doubleValue()`.

Una primera solución sin generics consiste en guardar directamente las coordenadas como `Number`. Es flexible, pero se pierde precisión de tipo: el compilador solo sabe que `x` e `y` son números, no si eran `Integer`, `Double` u otro subtipo concreto.

```java
public class PuntoNumber {
    private final Number x;
    private final Number y;

    public PuntoNumber(Number x, Number y) {
        this.x = x;
        this.y = y;
    }

    public Number getX() {
        return x;
    }

    public Number getY() {
        return y;
    }

    public double calcularDistanciaA(PuntoNumber otro) {
        double dx = x.doubleValue() - otro.x.doubleValue();
        double dy = y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

La segunda solución usa generics con una cota superior. Así se indica que el punto trabaja con un tipo concreto de número, y ese tipo queda reflejado en los getters y en las operaciones entre puntos.

```java
public class Punto<T extends Number> {
    private final T x;
    private final T y;

    public Punto(T x, T y) {
        this.x = x;
        this.y = y;
    }

    public T getX() {
        return x;
    }

    public T getY() {
        return y;
    }

    public double calcularDistanciaA(Punto<T> otro) {
        double dx = x.doubleValue() - otro.x.doubleValue();
        double dy = y.doubleValue() - otro.y.doubleValue();
        return Math.sqrt(dx * dx + dy * dy);
    }
}

Punto<Integer> p1 = new Punto<>(1, 2);
Punto<Integer> p2 = new Punto<>(4, 6);
System.out.println(p1.calcularDistanciaA(p2));
```

Por el `type erasure`, el tipo `T` se borra tras la compilación y se reemplaza por su cota superior. En este caso, como `T extends Number`, el tipo usado internamente tras el borrado es `Number`.

## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

### Respuesta

La solución con `Number` permite mezclar tipos de número dentro del mismo punto sin ningún problema para el compilador. Por ejemplo, se puede crear `new PuntoNumber(1, 2.5)`, donde `x` es un `Integer` e `y` es un `Double`. Eso puede ser cómodo, pero también es menos estricto.

```java
PuntoNumber mixto = new PuntoNumber(1, 2.5);
Number x = mixto.getX();
```

La solución genérica `Punto<T extends Number>` refuerza la consistencia del tipo. Si se declara `Punto<Integer>`, ambas coordenadas deben ser `Integer`; si se declara `Punto<Double>`, ambas deben ser `Double`. Si se intenta mezclar `Integer` y `Double`, Java puede inferir un tipo común como `Number` si no se especifica explícitamente, pero si se trabaja con `Punto<Integer>` o `Punto<Double>`, el compilador impide la mezcla.

```java
Punto<Integer> entero = new Punto<>(1, 2);
Integer xEntero = entero.getX();

Punto<Double> real = new Punto<>(1.0, 2.5);
Double xReal = real.getX();
```

La diferencia en los getters es clave. En `PuntoNumber`, `getX()` devuelve `Number`, por lo que se pierde el tipo concreto. En `Punto<T>`, `getX()` devuelve `T`, así que si se tiene un `Punto<Integer>`, devuelve `Integer`; si se tiene un `Punto<Double>`, devuelve `Double`. Esa es la ganancia real: no solo reutilización, sino chequeo estático más preciso.

## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.

### Respuesta

El problema del diseño original es que la interfaz obliga a recibir un `Punto` demasiado general. Por eso, dentro de `Punto2D` hay que comprobar si realmente se recibió un `Punto2D`, hacer un cast y lanzar una excepción si se recibió otra implementación. Ese diseño permite errores que podrían evitarse en compilación.

Se puede mejorar usando una interfaz genérica donde cada implementación indique cuál es el tipo de punto con el que sabe calcular distancias. Así, `Punto2D` implementa `Punto<Punto2D>` y `Punto3D` implementa `Punto<Punto3D>`.

```java
public interface Punto<T> {
    double distanciaA(T otro);
}

public class Punto2D implements Punto<Punto2D> {
    private final double x;
    private final double y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public double distanciaA(Punto2D otro) {
        return Math.sqrt(
            Math.pow(x - otro.x, 2) +
            Math.pow(y - otro.y, 2)
        );
    }
}

public class Punto3D implements Punto<Punto3D> {
    private final double x;
    private final double y;
    private final double z;

    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public double distanciaA(Punto3D otro) {
        return Math.sqrt(
            Math.pow(x - otro.x, 2) +
            Math.pow(y - otro.y, 2) +
            Math.pow(z - otro.z, 2)
        );
    }
}
```

Con esta versión, el compilador impide llamar a `distanciaA` mezclando dimensiones. Un `Punto2D` solo acepta otro `Punto2D`, y un `Punto3D` solo acepta otro `Punto3D`. Se evita `instanceof`, se evita downcasting y se mueve el error desde ejecución hacia compilación.

```java
Punto2D a = new Punto2D(0, 0);
Punto2D b = new Punto2D(3, 4);
System.out.println(a.distanciaA(b));

Punto3D c = new Punto3D(0, 0, 0);
// a.distanciaA(c); // Error de compilación
```

## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

### Respuesta

Aunque `String` sea subtipo de `Object`, `List<String>` no es subtipo de `List<Object>` en Java. Los genéricos son invariantes por defecto. Esto evita que se pueda tratar una lista de cadenas como si fuera una lista de objetos generales y luego insertar, por ejemplo, un `Integer` dentro de ella.

```java
List<String> textos = new ArrayList<>();
// List<Object> objetos = textos; // Error de compilación
```

Con arrays ocurre algo distinto: `String[]` sí es subtipo de `Object[]`. Los arrays en Java son covariantes. Eso permite asignar un array de cadenas a una referencia de array de objetos, pero mantiene una comprobación en tiempo de ejecución para impedir insertar un tipo incorrecto.

```java
String[] textos = new String[2];
Object[] objetos = textos;
objetos[0] = 42; // ArrayStoreException en ejecución
```

Un tipo genérico es covariante si mantiene la relación de subtipado: si `String` es subtipo de `Object`, entonces `Caja<String>` sería subtipo de `Caja<Object>`. Es contravariante si invierte la relación: `Caja<Object>` sería subtipo de `Caja<String>` en ciertos contextos. Es invariante si no hay relación de subtipado entre `Caja<String>` y `Caja<Object>`, aunque sí la haya entre `String` y `Object`.

## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

### Respuesta

Un wildcard es un comodín de tipo, representado con `?`, que significa “algún tipo desconocido”. Se usa cuando no se necesita nombrar exactamente el tipo concreto, pero sí se quiere expresar una restricción. Con `? extends T` se acepta `T` o cualquier subtipo de `T`; con `? super T` se acepta `T` o cualquier supertipo de `T`.

`List<? extends Number>` es útil cuando la lista produce valores numéricos que se van a leer. Puede ser una `List<Integer>`, una `List<Double>` o una `List<Number>`. Como no se conoce el subtipo exacto, no es seguro añadir elementos, salvo `null`. En cambio, `List<? super Integer>` es útil cuando se quieren consumir valores `Integer`, es decir, añadir enteros a una lista que puede ser de `Integer`, `Number` u `Object`.

```java
public static double sumar(List<? extends Number> numeros) {
    double suma = 0.0;
    for (Number numero : numeros) {
        suma += numero.doubleValue();
    }
    return suma;
}

List<Integer> enteros = List.of(1, 2, 3);
List<Double> reales = List.of(1.5, 2.5);

System.out.println(sumar(enteros));
System.out.println(sumar(reales));
```

```java
public static void agregarEnteros(List<? super Integer> destino) {
    destino.add(1);
    destino.add(2);
    destino.add(3);
}

List<Integer> enteros = new ArrayList<>();
List<Number> numeros = new ArrayList<>();
List<Object> objetos = new ArrayList<>();

agregarEnteros(enteros);
agregarEnteros(numeros);
agregarEnteros(objetos);
```

La regla práctica se suele resumir como PECS: Producer Extends, Consumer Super. Si una estructura produce valores para leer, suele usarse `extends`; si consume valores que se van a insertar, suele usarse `super`.
