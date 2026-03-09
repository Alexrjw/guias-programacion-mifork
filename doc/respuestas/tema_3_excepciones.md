<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Excepciones". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.

### Respuesta

En C, como no existe un mecanismo de excepciones integrado, el error debe comunicarse de forma manual. Una posibilidad es devolver un **valor centinela**, es decir, un valor especial que indique que algo ha ido mal. En este caso, como la raíz cuadrada de un número positivo nunca debe ser negativa, podría devolverse `-1.0f` para indicar error. Después, el código llamador comprueba ese valor y decide cómo informar al usuario desde fuera de la función.

La otra opción, más limpia y más escalable, consiste en **separar el resultado del estado de error**. Para ello, la función puede devolver un código de éxito o fracaso, y escribir el resultado correcto en un parámetro de salida. Así, la función no mezcla “dato calculado” con “señal de error”, algo especialmente útil cuando ya no es tan fácil elegir un valor centinela seguro.

**Opción 1: valor centinela**

```c
#include <stdio.h>
#include <math.h>

float raiz(float x) {
    if (x < 0.0f) {
        return -1.0f;   // valor especial que indica error
    }
    return sqrtf(x);
}

int main() {
    float n = -9.0f;
    float r = raiz(n);

    if (r == -1.0f) {
        printf("Error: no se puede calcular la raíz de un número negativo.\n");
    } else {
        printf("La raíz es %.2f\n", r);
    }

    return 0;
}
```

**Opción 2: código de estado + parámetro de salida**

```c
#include <stdio.h>
#include <math.h>

int raiz(float x, float *resultado) {
    if (x < 0.0f) {
        return 0;   // 0 = error
    }

    *resultado = sqrtf(x);
    return 1;       // 1 = éxito
}

int main() {
    float n = -9.0f;
    float r;

    if (raiz(n, &r)) {
        printf("La raíz es %.2f\n", r);
    } else {
        printf("Error: no se puede calcular la raíz de un número negativo.\n");
    }

    return 0;
}
```

---

## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

### Respuesta

Una **excepción** es un mecanismo para representar una situación anómala o incorrecta que aparece durante la ejecución de un programa. En lugar de devolver manualmente códigos de error, el lenguaje permite “lanzar” un objeto que describe el problema, de manera que la ejecución normal se interrumpe y se busca automáticamente un lugar donde tratar ese fallo.

Se usan con dos objetivos principales. Al **implementar** una función, sirven para indicar que no ha podido completarse correctamente una operación, por ejemplo porque un argumento es inválido o porque un fichero no existe. Al **llamar** a una función, permiten separar el código normal del código de tratamiento de errores, haciendo que el programa quede más claro y que los fallos puedan manejarse en el nivel más adecuado.

---

## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

### Respuesta

En Java, el método puede indicar el error lanzando una excepción en vez de devolver un valor especial. En este caso resulta natural usar `IllegalArgumentException`, porque pasar un número negativo a una raíz cuadrada viola la condición esperada por el método. Así, `raiz` queda encargada de detectar el problema, pero no de decidir cómo mostrarlo al usuario.

El control “desde fuera” se realiza en `main` mediante un bloque `try-catch`. El `try` contiene la llamada normal al método, y el `catch` recoge la excepción si se produce. De esta forma, se mantiene una separación muy clara entre el cálculo y la respuesta ante el error.

```java
public class Calculadora {

    public static double raiz(double x) {
        if (x < 0) {
            throw new IllegalArgumentException(
                "No se puede calcular la raíz de un número negativo: " + x
            );
        }
        return Math.sqrt(x);
    }

    public static void main(String[] args) {
        double n = -9;

        try {
            double r = raiz(n);
            System.out.println("La raíz es " + r);
        } catch (IllegalArgumentException e) {
            System.out.println("Error detectado desde main: " + e.getMessage());
        }
    }
}
```

---

## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

### Respuesta

**Lanzar** una excepción significa crearla y señalar que ha ocurrido un error, por ejemplo con `throw new IllegalArgumentException(...)`. **Capturar** o **controlar** una excepción significa interceptarla en un `catch` para decidir qué hacer con ella: mostrar un mensaje, recuperarse, registrar el fallo o lanzar otra distinta. **Propagarse** significa que, si el método actual no la captura, la excepción sube automáticamente al método que lo llamó, y así sucesivamente.

Mientras la excepción se va propagando, las llamadas intermedias de la pila se van abandonando. Es decir, esos métodos terminan de forma anormal y salen de la pila; no continúan por la instrucción siguiente como si nada hubiera pasado. En el ejemplo de la raíz, si `raiz(-9)` lanza una excepción y un método intermedio no la captura, ese método también termina inmediatamente. El sistema sigue subiendo hasta encontrar un `catch` compatible, por ejemplo en `main`.

Con esto se entiende una idea clave: los métodos por donde pasa la excepción **no se reanudan después**. Su ejecución normal queda interrumpida. Lo único que podría ejecutarse antes de salir definitivamente de uno de esos métodos sería un bloque `finally`, que precisamente existe para asegurar ciertas acciones de cierre o limpieza.

---

## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (*stack*) de llamadas?

### Respuesta

La principal ventaja es que evita tener que ir comprobando manualmente, en cada nivel, si una operación ha fallado. En C, si una función devuelve un código de error, cada función llamadora debe revisarlo, reenviarlo o traducirlo, lo que añade bastante código repetitivo. En Java, al propagarse la excepción por la pila de forma automática, sólo se captura donde realmente interese hacerlo.

Otra ventaja importante es que el error viaja con más información y con mejor contexto. No se transmite sólo un `0`, un `-1` o un valor especial, sino un objeto que puede indicar el tipo exacto de fallo, un mensaje descriptivo y el recorrido de llamadas por donde ha pasado. Eso mejora la legibilidad, reduce olvidos al tratar errores y hace más fácil depurar programas complejos.

---

## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

### Respuesta

Sí, en orientación a objetos las excepciones suelen ser **objetos**, y en Java lo son claramente. Eso significa que una excepción no es sólo una señal genérica de fallo, sino una entidad con su propio tipo, sus datos y su comportamiento heredado. Por ejemplo, una excepción puede llevar un mensaje, una causa interna o cualquier otro dato adicional que interese conservar.

Desde el punto de vista de la encapsulación, esto resulta muy útil porque toda la información relevante del error queda reunida en un único objeto bien definido. No hace falta repartir el estado del fallo entre códigos numéricos, variables globales y mensajes separados. Además, sí, se pueden crear **excepciones personalizadas**, heredando de `Exception` o de `RuntimeException`, para representar errores propios del dominio del problema.

---

## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

### Respuesta

La información esencial que aporta un objeto excepción al llegar a un manejador es, ante todo, **qué error ha ocurrido** y **de qué tipo es**. En Java eso se sabe por la clase concreta de la excepción, por ejemplo `IllegalArgumentException`, `IOException` o una excepción personalizada. Además, normalmente lleva un **mensaje descriptivo** que ayuda a entender el problema concreto.

Junto a eso, resulta muy valiosa la información de **dónde se produjo** y por qué camino llegó hasta allí. En Java esa pista aparece en la **traza de pila** (*stack trace*), que muestra los métodos por los que pasó el fallo. En muchos casos también puede llevar una **causa** asociada, es decir, otra excepción original que provocó la actual. Frente a C, esto supone una gran mejora porque el manejador recibe mucho más contexto sin necesidad de construirlo manualmente.

---

## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

### Respuesta

Sí, en Java se pueden tener varios bloques `catch` asociados al mismo `try`. Esto permite tratar de forma diferente varios tipos de excepción. Por ejemplo, una excepción de entrada/salida puede requerir una reacción distinta de una excepción por argumento inválido, y cada una puede tener su propio `catch`.

Sin embargo, cuando se produce una excepción, **sólo se ejecuta un bloque `catch`**: el primero que sea compatible con el tipo lanzado. Después de capturarla, los siguientes `catch` ya no se ejecutan. Por eso el orden es importante: primero deben colocarse los tipos más específicos y después los más generales.

---

## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

### Respuesta

Para garantizar que cierto código se ejecute siempre, incluso si aparece una excepción, se utiliza el bloque `finally`. Su finalidad clásica es cerrar ficheros, conexiones o cualquier recurso que deba liberarse ocurra lo que ocurra. Aunque se lance una excepción dentro del `try`, antes de salir del bloque se ejecuta el `finally`.

Puede usarse tanto cuando se captura la excepción con `catch` como cuando se decide no capturarla y dejar que siga propagándose. En ambos casos, el cierre del recurso se coloca en `finally`, de modo que no se pierde aunque el método termine de forma anormal.

**Ejemplo con `catch` y `finally`**

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class EjemploFinallyConCatch {
    public static void main(String[] args) {
        BufferedReader br = null;

        try {
            br = new BufferedReader(new FileReader("datos.txt"));
            System.out.println(br.readLine());
        } catch (IOException e) {
            System.out.println("Error al leer el fichero: " + e.getMessage());
        } finally {
            System.out.println("Entrando en finally...");
            if (br != null) {
                try {
                    br.close();
                    System.out.println("Fichero cerrado.");
                } catch (IOException e) {
                    System.out.println("Error al cerrar el fichero.");
                }
            }
        }
    }
}
```

**Ejemplo sin `catch`, dejando que se propague**

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class EjemploFinallySinCatch {

    public static void leerFichero() throws IOException {
        BufferedReader br = null;

        try {
            br = new BufferedReader(new FileReader("datos.txt"));
            System.out.println(br.readLine());
        } finally {
            System.out.println("Entrando en finally...");
            if (br != null) {
                br.close();
                System.out.println("Fichero cerrado.");
            }
        }
    }

    public static void main(String[] args) {
        try {
            leerFichero();
        } catch (IOException e) {
            System.out.println("La excepción llegó a main: " + e.getMessage());
        }
    }
}
```

---

## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

### Respuesta

Sí, el bloque `finally` puede aparecer sin `catch`; la estructura `try-finally` es perfectamente válida. En ese caso no se está capturando la excepción, sino simplemente asegurando que cierto código se ejecute antes de abandonar el bloque, tanto si la operación termina bien como si termina con error.

En condiciones normales, `finally` se ejecuta haya o no haya excepción, e incluso si dentro del `try` aparece un `return`. Lo que ocurre es que, antes de salir definitivamente del método, Java ejecuta primero el `finally`. Sólo en situaciones muy anómalas, como una parada brusca de la máquina virtual, podría no llegar a ejecutarse.

---

## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

### Respuesta

Las excepciones **controladas** (*checked*) son aquellas que el compilador obliga a tratar, bien capturándolas con `try-catch`, bien declarándolas con `throws`. Suelen representar fallos externos o previsibles que el programa podría querer recuperar. Las excepciones **no controladas** (*unchecked*) son las que heredan de `RuntimeException`, y el compilador no obliga a capturarlas ni a declararlas. Suelen representar errores de programación o usos incorrectos de una API.

`RuntimeException` marca precisamente esa frontera en Java. Por debajo de ella están excepciones como `IllegalArgumentException`, `NullPointerException` o una excepción personalizada como `DatoInvalidoException extends RuntimeException`. Entre las controladas podrían aparecer `IOException`, `FileNotFoundException` o una excepción propia como `ConfiguracionException extends Exception`, si se quiere obligar al llamador a tenerla en cuenta.

**Ejemplos típicos de excepciones controladas**

* `IOException`
* `FileNotFoundException`
* `ParseException`
* `ConfiguracionException extends Exception`

**Ejemplos típicos de excepciones no controladas**

* `IllegalArgumentException`
* `NullPointerException`
* `ArithmeticException`
* `DatoInvalidoException extends RuntimeException`

**Situaciones donde suele preferirse una excepción controlada**

* Al abrir un fichero que puede no existir.
* Al leer datos de red que pueden fallar por causas externas.
* Al cargar una configuración obligatoria desde disco.
* Al procesar un formato de entrada que puede venir mal pero es recuperable.

**Situaciones donde suele preferirse una excepción no controlada**

* Al pasar un argumento inválido a un método.
* Al usar un objeto en un estado incoherente.
* Al detectar un error lógico interno del programa.
* Al incumplir una precondición que el llamador debía respetar.

---

## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

### Respuesta

`throws` se escribe en la firma de un método para indicar que ese método puede terminar lanzando determinadas excepciones. Con ello no se está manejando el problema, sino declarando que, si ocurre, la responsabilidad de tratarlo quedará en el método llamador. Es una forma explícita de decir: “aquí no se resuelve este error; se deja subir”.

Por eso `throws` es una alternativa a capturar una excepción controlada. Ante una excepción *checked*, el método tiene dos caminos: o bien la captura con `try-catch`, o bien no la captura y la declara con `throws`. En el segundo caso, la excepción seguirá propagándose hacia arriba hasta que alguien la maneje o hasta que llegue al nivel superior del programa.

---

## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

### Respuesta

Aquí lo importante es ver que el método abre el fichero, pero no decide cómo reaccionar si algo falla. En lugar de capturar la excepción de apertura, la declara con `throws IOException`, de forma que el problema suba al llamador. Aun así, si el fichero llega a abrirse, conviene cerrarlo siempre, y para eso se usa `finally`.

Debe observarse además que `FileNotFoundException` es una subclase de `IOException`, así que basta con declarar `throws IOException`. El llamador podrá capturarla más arriba y decidir si muestra un mensaje, si prueba otro fichero o si termina el programa.

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class LectorFicheros {

    public static String leerPrimeraLinea(String ruta) throws IOException {
        BufferedReader br = null;

        try {
            br = new BufferedReader(new FileReader(ruta));
            return br.readLine();
        } finally {
            if (br != null) {
                br.close();
            }
        }
    }

    public static void main(String[] args) {
        try {
            String linea = leerPrimeraLinea("datos.txt");
            System.out.println(linea);
        } catch (IOException e) {
            System.out.println("No se pudo leer el fichero: " + e.getMessage());
        }
    }
}
```

---

## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

### Respuesta

Sí, técnicamente se pueden poner excepciones no controladas en `throws`, incluyendo `RuntimeException` o cualquiera de sus subclases. Java lo permite, aunque no es obligatorio, porque esas excepciones ya pueden propagarse sin necesidad de declararlas. Por tanto, no se hace por exigencia del compilador, sino más bien por claridad documental.

El método llamador no queda obligado a poner `try-catch` por ello. Aun así, a veces puede tener sentido capturarlas en ciertos puntos altos del programa, por ejemplo para registrar un fallo, mostrar un mensaje general o evitar que toda la aplicación termine de forma brusca. En otras palabras, con las no controladas el `try-catch` suele usarse por estrategia de diseño, no por obligación sintáctica.

---

## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

### Respuesta

Se recomienda usar excepciones **controladas** cuando el error es externo al código actual y tiene sentido que el llamador decida qué hacer. Un ejemplo claro es un fichero que no existe, una conexión que falla o una entrada que puede venir mal desde fuera. En cambio, se suele usar una excepción **no controlada** como `IllegalArgumentException` cuando el problema indica un mal uso del método o un error de programación, es decir, una precondición incumplida.

No todos los lenguajes distinguen ambas opciones como hace Java. De hecho, Java es uno de los ejemplos más conocidos por tener excepciones *checked* y *unchecked* claramente separadas. En muchos otros lenguajes, como C++, Python, JavaScript o C#, la opción más habitual es que las excepciones sean, en la práctica, **no controladas**, es decir, no obligan al compilador a capturarlas o declararlas.

---

## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

### Respuesta

Sí, tiene sentido lanzar excepciones dentro de un `catch`. Suele hacerse cuando se quiere **traducir** un error de bajo nivel a otro de más alto nivel y más cercano al problema real de la aplicación. Por ejemplo, un `IOException` al leer un fichero puede transformarse en una excepción de negocio como `ConfiguracionException`, para que el resto del programa no dependa de detalles internos de lectura.

También se puede **relanzar la misma excepción** capturada. Esto tiene sentido cuando en ese nivel sólo interesa hacer algo puntual, como registrar el error o liberar recursos adicionales, pero se quiere que el tratamiento real ocurra más arriba. En ese caso se captura, se hace esa acción auxiliar, y luego se vuelve a lanzar.

**Ejemplo lanzando otra excepción dentro del `catch`**

```java
import java.io.IOException;

class ConfiguracionException extends Exception {
    public ConfiguracionException(String mensaje, Throwable causa) {
        super(mensaje, causa);
    }
}

public class EjemploTraducirExcepcion {

    public static void cargarConfiguracion() throws ConfiguracionException {
        try {
            throw new IOException("No se pudo leer config.txt");
        } catch (IOException e) {
            throw new ConfiguracionException(
                "Error al cargar la configuración de la aplicación",
                e
            );
        }
    }
}
```

**Ejemplo relanzando la misma excepción**

```java
import java.io.IOException;

public class EjemploRelanzar {

    public static void leerDatos() throws IOException {
        try {
            throw new IOException("Fallo de lectura");
        } catch (IOException e) {
            System.out.println("Se registra el error y se relanza.");
            throw e;
        }
    }
}
```

---

## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

### Respuesta

Que una excepción sea la **causa** de otra significa que la segunda no apareció de manera aislada, sino como consecuencia de una excepción anterior. Esto es muy útil cuando se quiere ocultar un detalle técnico de bajo nivel hacia fuera, pero sin perder la información original. Así, la capa superior puede trabajar con una excepción más significativa, mientras que la causa conserva el motivo real del fallo.

Sí, cuando la excepción se muestra por pantalla con su traza, normalmente la causa **sí se ve**. En Java suele aparecer con una línea del tipo `Caused by: ...`, seguida de la excepción original y su propia parte de la traza. Eso permite entender tanto el error de alto nivel como el problema técnico concreto que lo provocó.

``` java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

class AccesoDatosException extends Exception {
    public AccesoDatosException(String mensaje, Throwable causa) {
        super(mensaje, causa);
    }
}

public class EjemploCausa {

    public static String leerUsuario(String ruta) throws AccesoDatosException {
        BufferedReader br = null;

        try {
            br = new BufferedReader(new FileReader(ruta));
            return br.readLine();
        } catch (IOException e) {
            throw new AccesoDatosException(
                "No se pudo obtener el usuario desde el sistema de datos",
                e
            );
        } finally {
            if (br != null) {
                try {
                    br.close();
                } catch (IOException e) {
                    System.out.println("No se pudo cerrar el fichero.");
                }
            }
        }
    }

    public static void main(String[] args) {
        try {
            leerUsuario("usuarios.txt");
        } catch (AccesoDatosException e) {
            e.printStackTrace();
        }
    }
}
```

Si se quiere, también se puede convertir todo esto en un formato de “apuntes para entregar”, con las respuestas todavía más limpias y listas para copiar.
