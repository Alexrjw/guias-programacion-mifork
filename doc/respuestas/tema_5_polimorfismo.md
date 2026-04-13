<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Polimorfismo". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones, Composición y Herencia.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

### Respuesta

El **polimorfismo** permite tratar objetos de distintos subtipos mediante una referencia común del supertipo y, aun así, que cada objeto responda con su comportamiento propio. Gracias a ello, puede escribirse código general que trabaje con una familia de objetos sin tener que preguntar continuamente de qué subtipo concreto es cada uno. En la práctica, sirve para extender programas con nuevos tipos sin rehacer toda la lógica común.

La **sobreescritura** de métodos consiste en que una subclase redefine un método heredado de la superclase manteniendo la misma cabecera básica. De ese modo, cuando se invoca ese método sobre un objeto de la subclase, se ejecuta la versión redefinida y no la original de la clase base. La sobreescritura es uno de los mecanismos principales que hacen posible el polimorfismo en orientación a objetos.

---

## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

### Respuesta

La **ligadura dinámica** o **enlace tardío** significa que la versión concreta de un método que se va a ejecutar no se decide solo por el tipo de la referencia, sino por el tipo real del objeto en tiempo de ejecución. Es decir, puede tenerse una referencia de tipo `Soldado`, pero si el objeto real es un `Zapador`, al invocar un método sobreescrito se ejecutará la versión de `Zapador`. Esa idea está directamente unida al polimorfismo, porque permite que una misma llamada produzca comportamientos distintos según el objeto real.

Esto depende del lenguaje. En **C++**, para que un método use despacho dinámico normalmente debe declararse como `virtual`; si no, la llamada puede resolverse estáticamente. En **Java**, los métodos de instancia normales usan despacho dinámico por defecto, salvo casos como `static`, `final` o `private`, así que no suele hacerse nada especial para activar el polimorfismo. En **Python**, el modelo es todavía más dinámico: las llamadas a métodos se resuelven en tiempo de ejecución de forma natural, sin tener que declarar algo equivalente a `virtual`.

---

## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

### Respuesta

En este ejemplo, `Soldado` define un saludo general y `Zapador` lo reemplaza por completo con uno propio. `Artillero`, en cambio, hereda el saludo tal como está. Lo importante no es solo la herencia, sino observar que el recorrido del array se hace con referencias de tipo `Soldado`, sin preguntar en cada caso cuál es el subtipo concreto.

Ahí se ve el polimorfismo en funcionamiento. Cuando se invoca `saludar()` sobre cada elemento del array, Java decide en tiempo de ejecución qué versión del método debe ejecutarse según el tipo real del objeto almacenado. Por ello, los `Zapador` saludarán de una forma y los `Artillero` de otra, aunque todos se estén tratando como `Soldado`.

```java
public class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("El nombre no puede estar vacío");
        }
        this.nombre = nombre;
    }

    public String getNombre() {
        return nombre;
    }

    public void saludar() {
        System.out.println("Soldado " + nombre + " saluda.");
    }
}
```

```java
public class Zapador extends Soldado {
    public Zapador(String nombre) {
        super(nombre);
    }

    @Override
    public void saludar() {
        System.out.println("Zapador en posición. Preparado para abrir paso.");
    }
}
```

```java
public class Artillero extends Soldado {
    public Artillero(String nombre) {
        super(nombre);
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Soldado[] soldados = {
            new Soldado("Luis"),
            new Zapador("Marta"),
            new Artillero("Carlos"),
            new Zapador("Elena")
        };

        for (Soldado s : soldados) {
            s.saludar();
        }
    }
}
```

---

## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

### Respuesta

Sí, al sobreescribir un método puede invocarse también la versión heredada de la superclase y añadir comportamiento antes o después. Esto resulta útil cuando no se quiere sustituir por completo lo que hacía la clase base, sino ampliarlo o ajustarlo ligeramente. Así se reutiliza parte del comportamiento original sin duplicar código.

En Java, la palabra clave que se utiliza para invocar el método de la clase base es **`super`**. En este caso, `Zapador` puede llamar primero a `super.saludar()` y después añadir su mensaje específico. De ese modo, el saludo general de `Soldado` se conserva y el subtipo incorpora su matiz propio.

```java
public class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Soldado " + nombre + " saluda.");
    }
}
```

```java
public class Zapador extends Soldado {
    public Zapador(String nombre) {
        super(nombre);
    }

    @Override
    public void saludar() {
        super.saludar();
        System.out.println("ZAPADOR A SUS ORDENES");
    }
}
```

---

## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

### Respuesta

Para que exista **sobreescritura** en Java, el método de la subclase debe mantener el mismo nombre y la misma lista de parámetros que el método heredado. El tipo de retorno debe ser el mismo o uno compatible más específico, lo que se conoce como retorno covariante. Además, no puede reducirse la visibilidad del método heredado y, respecto a excepciones comprobadas, no pueden declararse excepciones más amplias que las de la versión original.

La **sobrecarga** es distinta: consiste en tener varios métodos con el mismo nombre pero con parámetros diferentes dentro de una misma clase o jerarquía. La sobrecarga se resuelve en compilación; la sobreescritura, en cambio, participa en el polimorfismo y se resuelve en tiempo de ejecución. La anotación **`@Override`** sirve para indicar al compilador que se pretende sobreescribir un método heredado. Es recomendable usarla siempre porque ayuda a detectar errores muy comunes, como escribir mal el nombre, cambiar un parámetro sin querer o creer que se está sobreescribiendo cuando en realidad se está sobrecargando.

---

## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

### Respuesta

Sí, en Java suele empezarse a usar polimorfismo muy pronto, incluso antes de estudiarlo formalmente. En cuanto se redefine un método heredado y luego ese método puede invocarse sobre objetos de distintas clases, ya se está aprovechando la idea polimórfica. Muchas veces se usa primero de forma práctica y solo más tarde se le pone nombre teórico.

Por ejemplo, al sobreescribir `toString()` o `equals()`, ya se está usando polimorfismo. Ambos métodos vienen heredados de `Object`, y cuando una clase redefine su versión, Java ejecuta la implementación adecuada según el tipo real del objeto. De hecho, cuando se imprime un objeto con `System.out.println(objeto)`, suele llamarse implícitamente a `toString()`, y ahí ya está actuando el despacho dinámico.

---

## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

### Respuesta

Una **clase abstracta** es una clase pensada para servir de base a otras, pero no para crear objetos directamente de ella. Suele contener estado y comportamiento común, y además puede dejar algunos métodos sin implementación concreta. Un **método abstracto** es precisamente un método declarado sin cuerpo, obligando a que las subclases concretas lo implementen.

No puede crearse una instancia directa de una clase abstracta. En Java, debe escribirse `abstract` tanto en la declaración de la clase como en la del método abstracto. En este ejemplo, `Soldado` sería abstracta porque no tiene sentido crear un “soldado genérico” que ataque de una forma indefinida; lo correcto es que cada subtipo concreto indique cómo ataca.

```java
public abstract class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("El nombre no puede estar vacío");
        }
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Soldado " + nombre + " saluda.");
    }

    public String getNombre() {
        return nombre;
    }

    public abstract void atacar();
}
```

```java
public class Artillero extends Soldado {
    public Artillero(String nombre) {
        super(nombre);
    }

    @Override
    public void atacar() {
        System.out.println("El artillero dispara un cohete.");
    }
}
```

```java
public class Zapador extends Soldado {
    public Zapador(String nombre) {
        super(nombre);
    }

    @Override
    public void atacar() {
        System.out.println("El zapador coloca una mina.");
    }
}
```

---

## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

### Respuesta

En Java, un **método `final`** no puede ser sobreescrito por las subclases. Una **clase `final`** no puede ser heredada. Por tanto, `final` se utiliza para cerrar puntos de extensión: impide que otras clases cambien cierto comportamiento o continúen una jerarquía de herencia. Se trata de una decisión de diseño para proteger comportamiento, seguridad o inmutabilidad.

Esto se relaciona con el polimorfismo porque la sobreescritura es una de las bases del comportamiento polimórfico. Si un método es `final`, deja de poder redefinirse y, por tanto, se limita ese aspecto del polimorfismo. Si una clase es `final`, tampoco puede tener subclases que la especialicen. Un ejemplo muy conocido de la API estándar es **`String`**, que es una clase `final`.

---

## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

### Respuesta

Las **interfaces** son tipos que describen un conjunto de operaciones que una clase se compromete a ofrecer. Funcionan como un contrato: si una clase implementa una interfaz, debe proporcionar la implementación de los métodos que esa interfaz exige. En el estudio inicial, puede entenderse que una interfaz define “qué se puede hacer”, mientras que la clase concreta define “cómo se hace”.

Se parecen a las clases abstractas en que ambas sirven para generalizar y apoyar el polimorfismo, pero no son exactamente lo mismo. Una clase abstracta puede tener estado de instancia, constructores y métodos ya implementados; una interfaz, en el enfoque básico, se centra en el contrato común. Sí, una clase puede implementar **más de una interfaz**, y esa es una de las formas en que Java permite combinar varios comportamientos sin herencia múltiple de clases.

---

## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

### Respuesta

Aquí resulta útil hacer `Punto` abstracta y delegar en sus subclases el modo concreto de calcular distancias. Así, `Punto2D` sabe calcular la distancia a otro punto bidimensional y `Punto3D` a otro tridimensional. El método se declara en el tipo general `Punto`, pero su implementación depende del subtipo real. Esa es precisamente la idea polimórfica: `Linea` trabajará con referencias `Punto` sin conocer cuántas coordenadas tiene cada objeto.

Como se pide comprobar compatibilidad con `instanceof` y usar *downcasting*, cada implementación verificará que el otro punto pertenece al mismo subtipo. Si no es así, se lanzará una excepción. De ese modo, `Linea` puede recibir dos objetos `Punto` y calcular su longitud llamando a `origen.calcularDistanciaA(destino)`, sin conocer si son 2D o 3D. La decisión concreta queda delegada en el objeto real.

```java
public abstract class Punto {
    public abstract double calcularDistanciaA(Punto otro);
}
```

```java
public class Punto2D extends Punto {
    private double x;
    private double y;

    public Punto2D(double x, double y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public double calcularDistanciaA(Punto otro) {
        if (!(otro instanceof Punto2D)) {
            throw new IllegalArgumentException("Solo se puede calcular distancia entre puntos 2D");
        }

        Punto2D p = (Punto2D) otro;
        double dx = this.x - p.x;
        double dy = this.y - p.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

```java
public class Punto3D extends Punto {
    private double x;
    private double y;
    private double z;

    public Punto3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public double calcularDistanciaA(Punto otro) {
        if (!(otro instanceof Punto3D)) {
            throw new IllegalArgumentException("Solo se puede calcular distancia entre puntos 3D");
        }

        Punto3D p = (Punto3D) otro;
        double dx = this.x - p.x;
        double dy = this.y - p.y;
        double dz = this.z - p.z;
        return Math.sqrt(dx * dx + dy * dy + dz * dz);
    }
}
```

```java
public class Linea {
    private Punto origen;
    private Punto destino;

    public Linea(Punto origen, Punto destino) {
        if (origen == null || destino == null) {
            throw new IllegalArgumentException("Los puntos no pueden ser null");
        }
        this.origen = origen;
        this.destino = destino;
    }

    public double longitud() {
        return origen.calcularDistanciaA(destino);
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Linea l1 = new Linea(new Punto2D(0, 0), new Punto2D(3, 4));
        Linea l2 = new Linea(new Punto3D(0, 0, 0), new Punto3D(1, 2, 2));

        System.out.println("Longitud 2D: " + l1.longitud());
        System.out.println("Longitud 3D: " + l2.longitud());
    }
}
```

---

## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

### Respuesta

La **herencia de interfaces** consiste en que una interfaz puede extender a otra y, por tanto, heredar su contrato. Así, una interfaz más específica puede incluir todo lo que exigía la interfaz base y añadir nuevas operaciones. Esto permite construir jerarquías de capacidades: primero se define lo más general y después se especializa.

Sí, en Java existe **herencia múltiple de interfaces**. Una interfaz puede extender a varias interfaces a la vez, y una clase puede implementar varias interfaces simultáneamente. En el ejemplo pedido, `FicheroEscribible` extendería a `Fichero`, lo que significa que cualquier clase que implemente `FicheroEscribible` deberá saber leer, escribir y eliminar el fichero.

```java
public interface Fichero {
    String leer();
}
```

```java
public interface FicheroEscribible extends Fichero {
    void escribir(String contenido);
    void eliminar();
}
```

```java
public class FicheroMemoria implements FicheroEscribible {
    private String contenido = "";

    @Override
    public String leer() {
        return contenido;
    }

    @Override
    public void escribir(String contenido) {
        if (contenido == null) {
            throw new IllegalArgumentException("El contenido no puede ser null");
        }
        this.contenido = contenido;
    }

    @Override
    public void eliminar() {
        this.contenido = "";
    }
}
```

