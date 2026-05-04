# TEMA 7. Aspectos funcionales

## 1. ¿Qué es un puntero a una función? Pon un ejemplo de código en C, donde se define una función y que reciba una cadena de caracteres como parámetro y devuelva la cadena en mayúsculas. Crea un puntero en una variable local a dicha función llamado `aMayusculas` e invócala con el puntero.

### Respuesta

Un puntero a una función es una variable que almacena la dirección de una función. En C, igual que se puede guardar la dirección de un dato mediante un puntero, también se puede guardar la dirección de una función para invocarla más tarde de forma indirecta.

Esto permite pasar comportamiento como dato. Por ejemplo, una función puede recibir otra función como parámetro, o una variable local puede apuntar a una función concreta. La sintaxis de C es más incómoda que en lenguajes modernos, pero la idea conceptual es la misma: referenciar una operación para ejecutarla después.

```c
#include <stdio.h>
#include <ctype.h>
#include <string.h>

char* convertirAMayusculas(char* texto) {
    for (int i = 0; texto[i] != '\0'; i++) {
        texto[i] = toupper((unsigned char) texto[i]);
    }
    return texto;
}

int main() {
    char texto[] = "hola mundo";

    char* (*aMayusculas)(char*) = convertirAMayusculas;

    printf("%s\n", aMayusculas(texto));

    return 0;
}
```

En este ejemplo, `aMayusculas` no contiene una cadena, sino la dirección de una función que recibe `char*` y devuelve `char*`. Al escribir `aMayusculas(texto)`, se invoca la función apuntada por esa variable.

## 2. ¿Qué es una **función lambda** en un lenguaje de programación? Pon un ejemplo similar al anterior en Javascript y otro en Java con funciones lambda. Usa una variable local `aMayusculas` para apuntar a la función lambda. Por simplicidad, en Java, emplea `Function<String, String>` para el tipo de la referencia a la función lambda.

### Respuesta

Una función lambda es una función anónima, es decir, una función que puede definirse sin darle un nombre tradicional. Se suele usar para expresar comportamiento de forma breve, especialmente cuando se quiere guardar una operación en una variable, pasarla como parámetro o devolverla desde otra función.

En JavaScript, las funciones son valores de forma natural, por lo que una lambda puede guardarse directamente en una variable. En este ejemplo, `aMayusculas` contiene una función que recibe una cadena y devuelve esa cadena en mayúsculas.

```javascript
const aMayusculas = texto => texto.toUpperCase();

console.log(aMayusculas("hola mundo"));
```

En Java, una lambda necesita tener un tipo compatible, normalmente una interfaz funcional. Para este caso puede usarse `Function<String, String>`, que representa una función que recibe un `String` y devuelve un `String`.

```java
import java.util.function.Function;

public class EjemploLambda {
    public static void main(String[] args) {
        Function<String, String> aMayusculas = texto -> texto.toUpperCase();

        System.out.println(aMayusculas.apply("hola mundo"));
    }
}
```

## 3. ¿Qué es el **paradigma funcional**? ¿Por qué a algunos lenguajes orientados a objetos como Java 8, se les llama multi-paradigma? ¿Qué quiere decir que las funciones son "ciudadanos de primera clase"?

### Respuesta

El paradigma funcional es una forma de programar centrada en funciones, transformaciones de datos y composición de operaciones. En lugar de pensar principalmente en objetos que cambian de estado, se tiende a pensar en funciones que reciben entradas y producen salidas, idealmente evitando efectos secundarios innecesarios.

Un lenguaje como Java nació principalmente orientado a objetos, pero desde Java 8 incorporó lambdas, interfaces funcionales, streams y referencias a métodos. Por eso se dice que es multiparadigma: permite seguir programando con clases, objetos, herencia y polimorfismo, pero también permite usar técnicas propias del estilo funcional.

Que las funciones sean ciudadanos de primera clase significa que pueden tratarse como valores: guardarse en variables, pasarse como argumentos, devolverse desde otras funciones y almacenarse en estructuras de datos. En C se puede hacer algo parecido con punteros a función, pero en lenguajes modernos suele integrarse con más seguridad y expresividad.

## 4. Explica la sintaxis básica de una función lambda en Java.

### Respuesta

La sintaxis básica de una lambda en Java tiene parámetros a la izquierda, el operador `->` en el centro y el cuerpo de la función a la derecha. Su forma general es: `(parámetros) -> expresión` o `(parámetros) -> { bloque de instrucciones }`.

Si hay un solo parámetro, los paréntesis pueden omitirse. Si el cuerpo es una sola expresión, Java devuelve automáticamente el resultado de esa expresión. Si el cuerpo tiene varias instrucciones, se usan llaves y, si la función debe devolver un valor, se escribe `return` explícitamente.

```java
Function<String, String> aMayusculas = texto -> texto.toUpperCase();

Function<Integer, Integer> cuadrado = n -> n * n;

Function<Integer, Integer> dobleSiPositivo = n -> {
    if (n > 0) {
        return n * 2;
    }
    return n;
};
```

La lambda no vive sola: siempre necesita un tipo destino. Ese tipo destino debe ser una interfaz funcional, como `Function<T, R>`, `Predicate<T>`, `Consumer<T>` o una interfaz funcional creada manualmente.

## 5. Ahora recibamos una función como parámetro a un método y la llamaremos desde dentro. Amplia los ejemplos anteriores de Java y JavaScript con un método llamado `transformar`, que reciba un `String` como parámetro y luego una función transformadora como lo es `aMayúsculas` y la invoque desde dentro.

### Respuesta

Recibir una función como parámetro permite separar qué se quiere hacer de cuándo o dónde se aplica. La función `transformar` puede encargarse de recibir una cadena y aplicar la operación que se le pase, sin conocer de antemano si esa operación convierte a mayúsculas, invierte, recorta o modifica el texto de otra forma.

En JavaScript, esto es directo porque las funciones son valores normales del lenguaje.

```javascript
function transformar(texto, transformadora) {
    return transformadora(texto);
}

const aMayusculas = texto => texto.toUpperCase();

console.log(transformar("hola mundo", aMayusculas));
```

En Java se puede hacer lo mismo usando `Function<String, String>` como tipo del parámetro funcional. La función se invoca con el método `apply`.

```java
import java.util.function.Function;

public class EjemploTransformar {
    public static String transformar(String texto, Function<String, String> transformadora) {
        return transformadora.apply(texto);
    }

    public static void main(String[] args) {
        Function<String, String> aMayusculas = texto -> texto.toUpperCase();

        System.out.println(transformar("hola mundo", aMayusculas));
    }
}
```

## 6. Ahora, invoca `transformar`, con una nueva función lambda directamente en la llamada a `transformar`, por ejemplo, una función lambda que invierta la cadena. Define la función de inversión justo cuando la estás pasando como parámetro.

### Respuesta

Una lambda no necesita guardarse previamente en una variable. Puede definirse directamente en el lugar donde se pasa como argumento. Esto es útil cuando la operación es pequeña y solo se necesita una vez.

En JavaScript, se puede invocar `transformar` pasando la lambda directamente como segundo argumento. Esa lambda recibe el texto y devuelve una nueva cadena invertida.

```javascript
function transformar(texto, transformadora) {
    return transformadora(texto);
}

console.log(transformar("hola", texto => texto.split("").reverse().join("")));
```

En Java se puede hacer algo equivalente usando `Function<String, String>`. La clase `StringBuilder` permite invertir cadenas de forma sencilla.

```java
import java.util.function.Function;

public class EjemploTransformarDirecto {
    public static String transformar(String texto, Function<String, String> transformadora) {
        return transformadora.apply(texto);
    }

    public static void main(String[] args) {
        String resultado = transformar(
            "hola",
            texto -> new StringBuilder(texto).reverse().toString()
        );

        System.out.println(resultado);
    }
}
```

## 7. ¿Qué se entiende por cierre o "closure" en el contexto de las funciones lambda? Pon un ejemplo en Java de cómo una función lambda es capaz de acceder a una variable local en el contexto donde fue definida. Modifica el ejemplo anterior, creando otra función lambda para transformar una cadena, pero que lo que haga es concatenar a la cadena de entrada otra cadena que está en una variable local definida fuera de la función lambda.

### Respuesta

Un cierre, o closure, aparece cuando una función recuerda variables del contexto donde fue definida. La lambda no solo contiene código, sino que también puede capturar valores externos necesarios para ejecutarse correctamente más tarde.

En Java, una lambda puede acceder a variables locales externas siempre que sean finales o efectivamente finales. Esto significa que no hace falta escribir `final`, pero la variable no puede modificarse después de ser capturada.

```java
import java.util.function.Function;

public class EjemploClosure {
    public static String transformar(String texto, Function<String, String> transformadora) {
        return transformadora.apply(texto);
    }

    public static void main(String[] args) {
        String sufijo = " mundo";

        Function<String, String> concatenarSufijo = texto -> texto + sufijo;

        System.out.println(transformar("hola", concatenarSufijo));
    }
}
```

Aquí la lambda `texto -> texto + sufijo` usa `sufijo`, aunque `sufijo` no es un parámetro de la lambda. Ese valor pertenece al contexto externo donde la lambda fue creada. Esa captura del contexto es justamente la idea de closure.

## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

### Respuesta

Un puntero a función en C apunta a código ejecutable, pero no captura automáticamente el contexto local donde fue creado. Si se necesita asociar datos adicionales a una función, normalmente hay que pasarlos aparte, por ejemplo mediante un `void*` auxiliar o una estructura. Es más manual y menos seguro.

Una lambda moderna puede representar no solo una función, sino una función junto con datos capturados de su entorno. Por eso una lambda con closure se parece más a un pequeño objeto que contiene comportamiento y estado capturado. En Java, además, ese comportamiento se ajusta a una interfaz funcional con tipos comprobados por el compilador.

También hay una diferencia de nivel de abstracción. En C se trabaja cerca de direcciones de memoria y firmas de función. En Java o JavaScript se trabaja con valores funcionales integrados en el lenguaje, con sintaxis más expresiva y mejor integración con colecciones, métodos de orden superior y composición.

## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento.

### Respuesta

Una función puede devolver otra función. En este ejemplo, `crearDescuento` recibe un porcentaje y devuelve una función que aplica ese descuento a cualquier cantidad. La función devuelta queda especializada con el porcentaje recibido.

```java
import java.util.function.Function;

public class EjemploDescuento {
    public static Function<Double, Double> crearDescuento(double porcentaje) {
        return precio -> precio - precio * porcentaje / 100.0;
    }

    public static void main(String[] args) {
        Function<Double, Double> descuento10 = crearDescuento(10.0);
        Function<Double, Double> descuento25 = crearDescuento(25.0);

        System.out.println(descuento10.apply(100.0)); // 90.0
        System.out.println(descuento25.apply(100.0)); // 75.0
    }
}
```

La closure aparece porque la lambda `precio -> precio - precio * porcentaje / 100.0` usa la variable `porcentaje`, aunque esa variable pertenece al método `crearDescuento`. Cuando el método termina, la función devuelta sigue recordando el valor concreto de `porcentaje`.

Por eso `descuento10` y `descuento25` tienen comportamientos distintos aunque se crearon con el mismo código. Cada función conserva su propio valor capturado: una recuerda `10.0` y la otra recuerda `25.0`.

## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisitos tiene?

### Respuesta

Una interfaz funcional es una interfaz que tiene exactamente un método abstracto. Ese único método abstracto define la forma de la función: qué parámetros recibe y qué tipo devuelve. Una lambda puede asignarse a una variable de ese tipo si su firma coincide con la del método abstracto.

Puede tener métodos `default`, métodos `static` y métodos heredados de `Object`, pero solo debe tener un método abstracto propio. Para documentar y hacer que el compilador compruebe la intención, se suele usar la anotación `@FunctionalInterface`.

```java
@FunctionalInterface
public interface Transformador {
    String transformar(String texto);
}
```

Si se intentara añadir otro método abstracto a esa interfaz, dejaría de ser funcional y el compilador marcaría error si está anotada con `@FunctionalInterface`. Esa anotación no es obligatoria, pero es muy recomendable porque evita errores de diseño.

## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`).

### Respuesta

Se puede crear una interfaz funcional propia cuando se quiere dar un nombre de dominio a una operación. En este caso, `Transformador` representa cualquier operación que recibe una cadena y devuelve otra cadena.

```java
@FunctionalInterface
public interface Transformador {
    String transformar(String texto);
}
```

Luego se puede usar esa interfaz como tipo de una lambda. La lambda debe ser compatible con el método `transformar`: recibe un `String` y devuelve un `String`.

```java
public class EjemploTransformador {
    public static String transformar(String texto, Transformador transformador) {
        return transformador.transformar(texto);
    }

    public static void main(String[] args) {
        Transformador aMayusculas = texto -> texto.toUpperCase();

        System.out.println(transformar("hola", aMayusculas));
        System.out.println(transformar("hola", texto -> texto + " mundo"));
    }
}
```

La ventaja de una interfaz propia es que el código puede expresar mejor la intención. Aunque técnicamente se podría usar `Function<String, String>`, `Transformador` comunica que esa función forma parte del vocabulario del problema.

## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`.

### Respuesta

La interfaz puede generalizarse usando dos parámetros de tipo: uno para el tipo de entrada y otro para el tipo de salida. Así deja de estar limitada a transformar `String` en `String` y puede representar transformaciones entre cualquier par de tipos.

```java
@FunctionalInterface
public interface Transformador<T, R> {
    R transformar(T valor);
}
```

Con esta versión, un transformador de `Double` a `Integer` puede redondear un número real y devolver un entero. El compilador comprueba que la entrada y la salida coincidan con los tipos declarados.

```java
public class EjemploTransformadorGenerico {
    public static void main(String[] args) {
        Transformador<Double, Integer> redondear = valor -> (int) Math.round(valor);

        Integer resultado = redondear.transformar(3.7);
        System.out.println(resultado); // 4
    }
}
```

Esta interfaz ya se parece mucho a una abstracción funcional general: recibir un valor de tipo `T` y producir un valor de tipo `R`. Justamente por eso Java ya trae una interfaz estándar equivalente.

## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java.

### Respuesta

Java incluye muchas interfaces funcionales predefinidas en el paquete `java.util.function`. La más general para transformar un valor en otro es `Function<T, R>`, cuyo método principal es `R apply(T value)`. Es equivalente en intención al `Transformador<T, R>` creado manualmente.

Las interfaces más comunes son `Function<T, R>`, `Consumer<T>`, `Supplier<T>` y `Predicate<T>`. `Function` transforma un valor, `Consumer` consume un valor sin devolver resultado, `Supplier` produce un valor sin recibir argumentos y `Predicate` evalúa una condición booleana.

```java
import java.util.function.*;

Function<String, Integer> longitud = texto -> texto.length();
Consumer<String> imprimir = texto -> System.out.println(texto);
Supplier<Double> aleatorio = () -> Math.random();
Predicate<Integer> esPositivo = n -> n > 0;
```

También existen variantes especializadas para evitar boxing innecesario con primitivos, como `IntPredicate`, `IntFunction<R>`, `ToIntFunction<T>`, `IntConsumer`, `DoubleSupplier`, entre otras. Además, hay interfaces para dos argumentos, como `BiFunction<T, U, R>`, `BiConsumer<T, U>` y `BiPredicate<T, U>`.

## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo.

### Respuesta

El método `forEach` permite recorrer una colección pasando una acción que se aplicará a cada elemento. Esa acción se expresa mediante una lambda compatible con `Consumer`, porque consume cada elemento de la lista y no devuelve ningún resultado.

```java
import java.util.List;

public class EjemploForEach {
    public static void main(String[] args) {
        List<Integer> numeros = List.of(-2, 0, 3, 7, -1);

        numeros.forEach(numero -> {
            if (numero > 0) {
                System.out.println(numero + " es positivo");
            }
        });
    }
}
```

Este ejemplo es funcional en el sentido de que el recorrido se expresa pasando comportamiento al método `forEach`. Aun así, imprimir por pantalla es un efecto secundario, por lo que no todo uso de lambdas es automáticamente “funcional puro”.

## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora.

### Respuesta

`forEach` usa `Consumer<? super T>` porque el consumidor no produce elementos de tipo `T`, sino que recibe elementos de tipo `T` para hacer algo con ellos. Si una lista contiene `String`, es válido consumir esos `String` con un `Consumer<String>`, pero también con un `Consumer<Object>`, porque todo `String` puede tratarse como `Object`.

PECS significa Producer Extends, Consumer Super. Si una estructura produce valores que se van a leer, conviene usar `extends`. Si una estructura consume valores que se le van a entregar, conviene usar `super`. En `forEach`, el `Consumer` consume elementos de la lista, por eso se usa `? super T`.

```java
List<String> nombres = List.of("Ana", "Luis");

Consumer<Object> imprimirObjeto = objeto -> System.out.println(objeto);
nombres.forEach(imprimirObjeto); // Válido gracias a Consumer<? super String>
```

El método `transformar` también puede mejorarse con wildcards. Si transforma un valor de tipo `T` y devuelve un resultado de tipo `R`, la función transformadora puede consumir cualquier supertipo de `T` y producir cualquier subtipo de `R`. Una firma más flexible sería la siguiente.

```java
public static <T, R> R transformar(T valor, Function<? super T, ? extends R> transformadora) {
    return transformadora.apply(valor);
}
```

## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`.

### Respuesta

Una referencia a método permite usar un método existente como si fuera una función. En JavaScript hay que tener cuidado con el valor de `this`, porque al extraer un método de un objeto se puede perder el contexto de la instancia. Por eso suele usarse `bind`.

```javascript
class Persona {
    constructor(nombre) {
        this.nombre = nombre;
    }

    saludar() {
        return `Hola, soy ${this.nombre}`;
    }
}

const persona = new Persona("Ana");
const saludar = persona.saludar.bind(persona);

console.log(saludar());
```

En Java, una referencia a método se escribe con `::`. Si el método no recibe parámetros y devuelve un `String`, puede guardarse en un `Supplier<String>`.

```java
import java.util.function.Supplier;

public class EjemploReferenciaMetodo {
    static class Persona {
        private final String nombre;

        Persona(String nombre) {
            this.nombre = nombre;
        }

        String saludar() {
            return "Hola, soy " + nombre;
        }
    }

    public static void main(String[] args) {
        Persona persona = new Persona("Ana");
        Supplier<String> saludar = persona::saludar;

        System.out.println(saludar.get());
    }
}
```

## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia.

### Respuesta

Java permite varias formas de referencia a método. Todas usan `::`, pero cambian según se referencie un método estático, un constructor, un método de una instancia concreta o un método de instancia que se aplicará sobre cualquier objeto recibido como parámetro.

```java
import java.util.function.*;

public class EjemplosReferencias {
    static class Persona {
        private final String nombre;

        Persona(String nombre) {
            this.nombre = nombre;
        }

        String saludar() {
            return "Hola, soy " + nombre;
        }

        String getNombre() {
            return nombre;
        }
    }

    public static void main(String[] args) {
        // 1. Método estático
        Function<String, Integer> convertirEntero = Integer::parseInt;
        System.out.println(convertirEntero.apply("42"));

        // 2. Constructor
        Function<String, Persona> crearPersona = Persona::new;
        Persona ana = crearPersona.apply("Ana");

        // 3. Método de instancia de una instancia concreta
        Supplier<String> saludarAna = ana::saludar;
        System.out.println(saludarAna.get());

        // 4. Método de instancia sobre cualquier instancia
        Function<Persona, String> obtenerNombre = Persona::getNombre;
        System.out.println(obtenerNombre.apply(ana));
    }
}
```

La cuarta forma suele ser la menos intuitiva al principio. `Persona::getNombre` no apunta al método de una persona concreta, sino a una operación que recibirá una `Persona` y ejecutará `getNombre` sobre ella.

## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`.

### Respuesta

Para ordenar objetos propios, Java necesita saber cómo comparar dos instancias. Con `Collections.sort`, se puede pasar un `Comparator<Persona>` que indique cuándo una persona debe ir antes que otra. En este caso se ordena primero por edad y, si la edad coincide, por nombre.

Una primera versión puede escribir la comparación manualmente dentro de la lambda. Se compara primero con `Integer.compare`; si el resultado no es cero, se devuelve. Si las edades son iguales, se comparan los nombres con `compareTo`.

```java
import java.util.*;

public class EjemploOrdenManual {
    static class Persona {
        private final String nombre;
        private final int edad;

        Persona(String nombre, int edad) {
            this.nombre = nombre;
            this.edad = edad;
        }

        String getNombre() { return nombre; }
        int getEdad() { return edad; }

        @Override
        public String toString() {
            return nombre + " (" + edad + ")";
        }
    }

    public static void main(String[] args) {
        List<Persona> personas = new ArrayList<>(List.of(
            new Persona("Luis", 30),
            new Persona("Ana", 25),
            new Persona("Carlos", 30)
        ));

        Collections.sort(personas, (p1, p2) -> {
            int comparacionEdad = Integer.compare(p1.getEdad(), p2.getEdad());
            if (comparacionEdad != 0) {
                return comparacionEdad;
            }
            return p1.getNombre().compareTo(p2.getNombre());
        });

        System.out.println(personas);
    }
}
```

La segunda versión usa los métodos de fábrica de `Comparator`, que expresan mejor la intención y reducen código accidental. Primero se compara por edad y luego, en caso de empate, por nombre.

```java
Collections.sort(
    personas,
    Comparator.comparingInt(Persona::getEdad)
              .thenComparing(Persona::getNombre)
);
```

La versión con `Comparator` es normalmente preferible porque es más declarativa: dice “ordenar por edad y luego por nombre” en vez de detallar manualmente cada paso de comparación. La versión manual sigue siendo útil para entender qué está ocurriendo por debajo, pero para código real conviene elegir la forma más expresiva y menos propensa a errores.
