<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Composición". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación y Excepciones.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.1. Composición

## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea.

### Respuesta

En C, la composición se suele expresar incluyendo una estructura dentro de otra. En este caso, una `Linea` **tiene dos** `Punto`, y cada `Punto` guarda sus coordenadas `x` e `y`. Se trata de una relación muy natural para modelar objetos geométricos: primero se define la pieza pequeña (`Punto`) y después la estructura mayor (`Linea`) que la contiene.

La distancia entre dos puntos puede calcularse con la fórmula euclídea: raíz cuadrada de la suma de los cuadrados de las diferencias en `x` e `y`. A partir de ahí, la longitud de una línea no es más que la distancia entre sus dos extremos. Así se observa cómo una función de más alto nivel reutiliza otra más básica, apoyándose en la composición de estructuras.

```c
#include <stdio.h>
#include <math.h>

typedef struct {
    double x;
    double y;
} Punto;

typedef struct {
    Punto inicio;
    Punto fin;
} Linea;

double distancia(Punto a, Punto b) {
    double dx = b.x - a.x;
    double dy = b.y - a.y;
    return sqrt(dx * dx + dy * dy);
}

double longitudLinea(Linea l) {
    return distancia(l.inicio, l.fin);
}

int main() {
    Punto p1 = {1.0, 2.0};
    Punto p2 = {4.0, 6.0};
    Linea l = {p1, p2};

    printf("Distancia entre puntos: %.2f\n", distancia(p1, p2));
    printf("Longitud de la linea: %.2f\n", longitudLinea(l));

    return 0;
}
```

---

## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea.

### Respuesta

En Java, la misma idea se puede expresar con clases. La clase `Punto` representa un punto del plano y la clase `Linea` representa una línea formada por dos puntos. La diferencia importante con respecto a C es que, gracias a la encapsulación, se puede impedir que los datos internos se modifiquen libremente desde fuera.

Para lograr la inmutabilidad, los atributos deben declararse `private` y `final`, y no deben existir métodos que cambien su valor. Así, un `Punto` una vez creado no puede alterar sus coordenadas, y una `Linea` una vez construida no puede cambiar sus extremos. Este diseño hace que los objetos sean más seguros y fáciles de razonar, porque su estado permanece estable durante toda su vida.

```java
public final class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() {
        return x;
    }

    public double getY() {
        return y;
    }

    public double distancia(Punto otro) {
        double dx = otro.x - this.x;
        double dy = otro.y - this.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

```java
public final class Linea {
    private final Punto inicio;
    private final Punto fin;

    public Linea(Punto inicio, Punto fin) {
        if (inicio == null || fin == null) {
            throw new IllegalArgumentException("Los puntos no pueden ser null");
        }
        this.inicio = inicio;
        this.fin = fin;
    }

    public Punto getInicio() {
        return inicio;
    }

    public Punto getFin() {
        return fin;
    }

    public double longitud() {
        return inicio.distancia(fin);
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Punto p1 = new Punto(1, 2);
        Punto p2 = new Punto(4, 6);
        Linea l = new Linea(p1, p2);

        System.out.println("Longitud: " + l.longitud());
    }
}
```

---

## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`.

### Respuesta

La multiplicidad indica cuántos objetos de una clase pueden estar relacionados con un objeto de otra clase. Sirve para describir la cantidad permitida en una relación: uno, varios, ninguno o un rango determinado. Es una forma de especificar con precisión la estructura del modelo, no solo de decir que dos clases están conectadas.

En el ejemplo, una `Linea` está formada por exactamente dos `Punto`, por lo que la multiplicidad de `Linea` a `Punto` es **2**. En cambio, un `Punto` puede pertenecer a ninguna línea, a una sola o a muchas, porque el mismo punto geométrico podría reutilizarse en distintas líneas. Por ello, la multiplicidad de `Punto` a `Linea` sería **0..***.

Dicho de forma completa: desde `Linea` hacia `Punto`, la relación es **2**; desde `Punto` hacia `Linea`, la relación es **0..***. La primera está fijada por el propio diseño de la clase `Linea`, mientras que la segunda depende de cuántas líneas usen ese punto.

---

## 4. ¿Qué significa composición **fuerte** y composición **débil**? ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente.

### Respuesta

La composición fuerte se da cuando el objeto contenido forma parte esencial del objeto contenedor y su ciclo de vida queda ligado al de este. En otras palabras, el objeto interno nace y muere con el objeto que lo contiene. No suele tener sentido fuera de él, o al menos se considera que pertenece exclusivamente a ese contenedor.

La composición débil ocurre cuando un objeto se relaciona con otro, pero mantiene su existencia independiente. El contenedor puede referirse a él, usarlo o agruparlo, pero ese objeto puede seguir existiendo aunque el contenedor desaparezca. Por eso, en este caso el ciclo de vida no está ligado.

Habitualmente, a la relación débil se la llama **asociación** o **agregación**, mientras que a la relación fuerte se la llama **composición** en sentido estricto. La idea clave es la dependencia del ciclo de vida: si ambos viven y mueren juntos, se habla de composición fuerte; si no, se trata de una relación más débil.

---

## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

### Respuesta

En esos casos no se suele hablar de composición, sino de **dependencia**. Una clase depende de otra cuando la utiliza de forma puntual para realizar alguna operación, pero no la conserva como parte estable de su estado interno. La relación existe durante una llamada, una variable local o una creación temporal, pero no define la estructura permanente del objeto.

La composición, en cambio, implica que una clase **tiene** otra como parte de sus atributos. Es una relación estructural y estable, no un uso momentáneo. Por eso, recibir un objeto como parámetro, devolverlo, declararlo dentro de un método o crearlo localmente con `new` son ejemplos típicos de dependencia y no de composición.

---

## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

### Respuesta

Para modelar una composición fuerte, conviene hacer que `Linea` cree internamente sus puntos a partir de datos simples, por ejemplo coordenadas. Así, los puntos nacen dentro de la línea y no dependen de objetos externos ya existentes. Conceptualmente, esos puntos pertenecen a esa línea concreta y su vida está ligada a la de ella.

Para modelar una composición débil, `Linea` puede recibir dos objetos `Punto` ya creados fuera. En ese caso, la línea solo mantiene referencias a ellos, pero esos puntos podrían seguir existiendo aunque la línea desaparezca. Esa independencia es precisamente lo que distingue la relación débil de la fuerte.

```java
public final class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double distancia(Punto otro) {
        double dx = otro.x - this.x;
        double dy = otro.y - this.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

```java
// Composición fuerte: Linea crea sus propios puntos
public final class LineaFuerte {
    private final Punto inicio;
    private final Punto fin;

    public LineaFuerte(double x1, double y1, double x2, double y2) {
        this.inicio = new Punto(x1, y1);
        this.fin = new Punto(x2, y2);
    }

    public double longitud() {
        return inicio.distancia(fin);
    }
}
```

```java
// Composición débil: Linea usa puntos creados fuera
public final class LineaDebil {
    private final Punto inicio;
    private final Punto fin;

    public LineaDebil(Punto inicio, Punto fin) {
        if (inicio == null || fin == null) {
            throw new IllegalArgumentException("Los puntos no pueden ser null");
        }
        this.inicio = inicio;
        this.fin = fin;
    }

    public double longitud() {
        return inicio.distancia(fin);
    }
}
```

---

## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

### Respuesta

En Java no se destruyen objetos de forma explícita como en otros lenguajes. La liberación de memoria la realiza automáticamente el recolector de basura o *garbage collector*. Por eso, aunque conceptualmente se diga que en una composición fuerte el contenedor “destruye” a sus componentes, en realidad lo que ocurre es que esos objetos quedan sin referencias accesibles cuando desaparece el contenedor.

Si una `Linea` es el único objeto que referencia a sus `Punto`, cuando esa `Linea` deja de ser alcanzable también dejan de ser alcanzables esos puntos. En ese momento, el recolector de basura podrá liberar su memoria más adelante, cuando lo considere oportuno. No hay una instrucción manual de destrucción porque Java está diseñado para gestionar esa tarea automáticamente.

Así, la relación de ciclo de vida en Java debe entenderse de forma lógica, no manual. No se escribe código para “borrar” los objetos contenidos, pero si solo existen dentro del contenedor, su existencia práctica termina cuando el contenedor deja de poder usarlos.

---

## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

### Respuesta

Aquí se está ante una composición débil porque los objetos `Profesor` pueden existir independientemente del `Departamento`. El departamento los agrupa y además guarda una referencia especial al director, pero esos profesores no nacen necesariamente dentro del departamento ni dependen de él para existir. La dificultad principal no está en la sintaxis, sino en mantener la invariante: siempre debe existir un director y ese director siempre debe pertenecer a la lista de profesores.

Para respetar la encapsulación, no debe devolverse el array interno ni permitir acceso directo a él. En su lugar, se ofrecen operaciones controladas: añadir al final, consultar cuántos profesores hay, obtener uno por posición, cambiar el director y eliminar por posición. Al eliminar, no puede romperse la invariante; por ello, si se intenta borrar al director, primero debe nombrarse automáticamente a otro profesor como nuevo director, salvo que fuera el único profesor, caso en el que debe lanzarse una excepción.

```java
public class Profesor {
    private final String nombre;

    public Profesor(String nombre) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("El nombre no puede estar vacio");
        }
        this.nombre = nombre;
    }

    public String getNombre() {
        return nombre;
    }
}
```

```java
public class Departamento {
    private static final int MAX_PROFESORES = 50;

    private final String nombre;
    private final Profesor[] profesores;
    private int numProfesores;
    private Profesor director;

    public Departamento(String nombre, Profesor directorInicial) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("El nombre del departamento no puede estar vacio");
        }
        if (directorInicial == null) {
            throw new IllegalArgumentException("Debe existir un director inicial");
        }

        this.nombre = nombre;
        this.profesores = new Profesor[MAX_PROFESORES];
        this.numProfesores = 0;

        this.profesores[0] = directorInicial;
        this.numProfesores = 1;
        this.director = directorInicial;
    }

    public String getNombre() {
        return nombre;
    }

    public Profesor getDirector() {
        return director;
    }

    public int getNumProfesores() {
        return numProfesores;
    }

    public Profesor getProfesor(int pos) {
        comprobarPosicion(pos);
        return profesores[pos];
    }

    public void addProfesor(Profesor p) {
        if (p == null) {
            throw new IllegalArgumentException("No se puede anadir un profesor null");
        }
        if (numProfesores >= MAX_PROFESORES) {
            throw new IllegalStateException("No caben mas profesores");
        }
        if (contieneProfesor(p)) {
            throw new IllegalArgumentException("Ese profesor ya pertenece al departamento");
        }

        profesores[numProfesores] = p;
        numProfesores++;
    }

    public void setDirector(Profesor nuevoDirector) {
        if (nuevoDirector == null) {
            throw new IllegalArgumentException("El director no puede ser null");
        }
        if (!contieneProfesor(nuevoDirector)) {
            throw new IllegalArgumentException("El director debe pertenecer al departamento");
        }
        this.director = nuevoDirector;
    }

    public void removeProfesor(int pos) {
        comprobarPosicion(pos);

        Profesor aEliminar = profesores[pos];

        if (numProfesores == 1) {
            throw new IllegalStateException("No se puede dejar el departamento sin director ni profesores");
        }

        if (aEliminar == director) {
            int nuevaPos = (pos == 0) ? 1 : 0;
            director = profesores[nuevaPos];
        }

        for (int i = pos; i < numProfesores - 1; i++) {
            profesores[i] = profesores[i + 1];
        }

        profesores[numProfesores - 1] = null;
        numProfesores--;
    }

    private void comprobarPosicion(int pos) {
        if (pos < 0 || pos >= numProfesores) {
            throw new IndexOutOfBoundsException("Posicion no valida");
        }
    }

    private boolean contieneProfesor(Profesor p) {
        for (int i = 0; i < numProfesores; i++) {
            if (profesores[i] == p) {
                return true;
            }
        }
        return false;
    }
}
```

---

## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

### Respuesta

Con `List`, buena parte del trabajo mecánico desaparece. Ya no hace falta desplazar manualmente los elementos al borrar, ni mantener un contador separado, ni preocuparse por el tamaño físico del array. La colección se encarga del almacenamiento dinámico y ofrece operaciones ya preparadas para añadir, eliminar y consultar por posición. Por tanto, el código queda más corto, más claro y menos propenso a errores.

Sin embargo, el problema de la encapsulación sigue existiendo. Si se devolviera directamente la lista interna de profesores, el código cliente podría modificarla desde fuera, añadiendo o quitando profesores sin pasar por las comprobaciones del departamento. Eso rompería la invariante del director. La solución habitual es devolver una vista no modificable o una copia defensiva, de modo que se pueda consultar la lista, pero no alterar el estado interno del objeto.

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Departamento {
    private static final int MAX_PROFESORES = 50;

    private final String nombre;
    private final List<Profesor> profesores;
    private Profesor director;

    public Departamento(String nombre, Profesor directorInicial) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("El nombre del departamento no puede estar vacio");
        }
        if (directorInicial == null) {
            throw new IllegalArgumentException("Debe existir un director inicial");
        }

        this.nombre = nombre;
        this.profesores = new ArrayList<>();
        this.profesores.add(directorInicial);
        this.director = directorInicial;
    }

    public String getNombre() {
        return nombre;
    }

    public Profesor getDirector() {
        return director;
    }

    public int getNumProfesores() {
        return profesores.size();
    }

    public Profesor getProfesor(int pos) {
        return profesores.get(pos);
    }

    public void addProfesor(Profesor p) {
        if (p == null) {
            throw new IllegalArgumentException("No se puede anadir un profesor null");
        }
        if (profesores.size() >= MAX_PROFESORES) {
            throw new IllegalStateException("No caben mas profesores");
        }
        if (profesores.contains(p)) {
            throw new IllegalArgumentException("Ese profesor ya pertenece al departamento");
        }

        profesores.add(p);
    }

    public void setDirector(Profesor nuevoDirector) {
        if (nuevoDirector == null) {
            throw new IllegalArgumentException("El director no puede ser null");
        }
        if (!profesores.contains(nuevoDirector)) {
            throw new IllegalArgumentException("El director debe pertenecer al departamento");
        }

        this.director = nuevoDirector;
    }

    public void removeProfesor(int pos) {
        Profesor aEliminar = profesores.get(pos);

        if (profesores.size() == 1) {
            throw new IllegalStateException("No se puede dejar el departamento sin director ni profesores");
        }

        if (aEliminar == director) {
            int nuevaPos = (pos == 0) ? 1 : 0;
            director = profesores.get(nuevaPos);
        }

        profesores.remove(pos);
    }

    public List<Profesor> getProfesores() {
        return Collections.unmodifiableList(profesores);
    }
}
```

El código original se ahorra, sobre todo, el array fijo, el atributo `numProfesores`, el desplazamiento manual al eliminar y parte de las comprobaciones de índices y capacidad física. Aun así, sigue siendo necesario controlar las invariantes de negocio. En cuanto a devolver todos los profesores, no debe devolverse la lista interna directamente; debe devolverse `Collections.unmodifiableList(profesores)` o una copia como `new ArrayList<>(profesores)`.

---

## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas.

### Respuesta

Una composición recursiva aparece cuando una clase contiene una referencia a otra instancia de su misma clase. En este caso, una `Persona` tiene una madre, que también es una `Persona`. Es una relación recursiva porque la definición vuelve a la propia clase. Este tipo de diseño es muy útil para representar árboles genealógicos, estructuras jerárquicas o cadenas de causas.

Para mantener la inmutabilidad, los atributos deben ser `private final` y no debe existir ningún método que modifique el estado una vez creado el objeto. Como no toda persona tiene por qué tener madre conocida en el modelo, puede permitirse `null` en el atributo `madre`. Así se puede construir una cadena desde el nieto hasta la abuela de manera sencilla y segura.

```java
public final class Persona {
    private final String nombre;
    private final Persona madre;

    public Persona(String nombre, Persona madre) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("El nombre no puede estar vacio");
        }
        this.nombre = nombre;
        this.madre = madre;
    }

    public String getNombre() {
        return nombre;
    }

    public Persona getMadre() {
        return madre;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Persona abuela = new Persona("Carmen", null);
        Persona madre = new Persona("Laura", abuela);
        Persona nieto = new Persona("Daniel", madre);

        System.out.println("Nieto: " + nieto.getNombre());
        System.out.println("Madre: " + nieto.getMadre().getNombre());
        System.out.println("Abuela: " + nieto.getMadre().getMadre().getNombre());
    }
}
```

Otros ejemplos clásicos de composición recursiva son un directorio que contiene subdirectorios, un nodo de árbol binario que contiene otros nodos, una categoría de productos que contiene subcategorías o una excepción que contiene otra excepción como causa. En todos esos casos, la estructura se construye repitiendo el mismo tipo dentro de sí mismo.

---

## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

### Respuesta

Una relación bidireccional es aquella en la que ambas clases conocen la existencia de la otra. No solo `Departamento` sabría qué `Profesor` tiene, sino que también cada `Profesor` sabría a qué `Departamento` pertenece. En una relación unidireccional, en cambio, solo uno de los dos lados mantiene la referencia.

Para implementar esto en el ejemplo, habría que añadir en `Profesor` un atributo de tipo `Departamento` y gestionarlo cuidadosamente. Cada vez que se añadiera un profesor a un departamento, habría que actualizar también el departamento almacenado dentro del profesor. Del mismo modo, al eliminarlo, habría que borrar o reajustar esa referencia. Lo importante es mantener la consistencia en ambos sentidos: no puede ocurrir que el departamento diga que un profesor pertenece a él y que el profesor diga que pertenece a otro o a ninguno.

Además, este tipo de relaciones obliga a tener más cuidado con las invariantes y con los métodos de modificación. No bastaría con cambiar solo un lado. Habría que centralizar las operaciones, por ejemplo con métodos como `addProfesor` y `removeProfesor`, para garantizar que ambas referencias se actualicen siempre juntas y no aparezcan estados incoherentes.

Si quieres, en el siguiente mensaje te lo puedo dejar también **con un estilo más “de entregar”**, todavía más formal y pulido, manteniendo exactamente el contenido.