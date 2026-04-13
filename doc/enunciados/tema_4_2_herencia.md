<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

### Respuesta

La **herencia** permite definir una clase nueva a partir de otra ya existente. Se utiliza cuando se cumple una relación del tipo **“A es-un B”**, es decir, cuando el subtipo es un caso particular del supertipo. Así, un `Artillero` es-un `Soldado`, y un `Zapador` también es-un `Soldado`. No se trata solo de reutilizar código, sino de expresar una relación conceptual correcta entre tipos.

De esa relación se derivan dos consecuencias principales. La primera es la **compatibilidad de tipos**: un objeto de una subclase puede tratarse como si fuera del tipo de su superclase, por lo que un `Artillero` o un `Zapador` pueden guardarse en una variable o en un array de `Soldado`. La segunda es la **herencia de estado y comportamiento**: las subclases reciben los atributos y métodos de la clase base. En este ejemplo, heredan el nombre y el método `saludar()`, y además añaden su propio estado específico, como el número de cohetes o de minas.

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
        System.out.println("Hola, soy " + nombre);
    }
}
```

```java
public class Artillero extends Soldado {
    private int numCohetes;

    public Artillero(String nombre, int numCohetes) {
        super(nombre);
        if (numCohetes < 0) {
            throw new IllegalArgumentException("El número de cohetes no puede ser negativo");
        }
        this.numCohetes = numCohetes;
    }

    public int getNumCohetes() {
        return numCohetes;
    }
}
```

```java
public class Zapador extends Soldado {
    private int numMinas;

    public Zapador(String nombre, int numMinas) {
        super(nombre);
        if (numMinas < 0) {
            throw new IllegalArgumentException("El número de minas no puede ser negativo");
        }
        this.numMinas = numMinas;
    }

    public int getNumMinas() {
        return numMinas;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Soldado[] ejercito = new Soldado[4];
        ejercito[0] = new Artillero("Luis", 5);
        ejercito[1] = new Zapador("Marta", 3);
        ejercito[2] = new Artillero("Carlos", 8);
        ejercito[3] = new Zapador("Elena", 2);

        for (Soldado s : ejercito) {
            s.saludar();
        }
    }
}
```

---

## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre?

### Respuesta

Cuando se crea un objeto de una subclase, no se ejecuta solo el constructor de esa subclase. También debe ejecutarse el constructor de la superclase, porque la parte heredada del objeto también tiene que inicializarse. Por tanto, al construir un `Artillero` o un `Zapador`, se ejecutan **dos constructores**: primero el de `Soldado` y después el de la subclase correspondiente.

La palabra `super` dentro de un constructor se utiliza para invocar el constructor de la clase base. Esa llamada debe hacerse al principio del constructor de la subclase, de forma explícita o implícita. Si la superclase no tiene visible un constructor sin parámetros, entonces no basta con dejar que Java inserte `super()` automáticamente: debe escribirse una llamada explícita a un constructor válido de la clase base, por ejemplo `super(nombre)`. En ese caso, sí, debe llamarse a `super` siempre de forma adecuada.

```java
public class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
        System.out.println("Constructor de Soldado");
    }
}
```

```java
public class Artillero extends Soldado {
    private int numCohetes;

    public Artillero(String nombre, int numCohetes) {
        super(nombre); // llamada al constructor de la superclase
        this.numCohetes = numCohetes;
        System.out.println("Constructor de Artillero");
    }
}
```

---

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

### Respuesta

Sí, los atributos privados de la superclase **forman parte** de la instancia de la subclase en memoria. Un objeto `Artillero` no contiene solo su campo `numCohetes`, sino también la parte heredada de `Soldado`, incluido su atributo `nombre`. Es decir, físicamente el objeto contiene tanto el estado definido en la clase base como el estado definido en la subclase.

Sin embargo, que ese atributo exista en memoria dentro del objeto no significa que pueda usarse directamente desde el código de la subclase. Si `nombre` es `private` en `Soldado`, entonces `Artillero` no puede escribir `nombre` ni leerlo directamente. Solo puede accederse a él mediante métodos accesibles de la superclase, por ejemplo un getter público o protegido. Por tanto, la presencia en memoria y la visibilidad en el código son cuestiones distintas.

```java
public class Soldado {
    private String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public String getNombre() {
        return nombre;
    }
}
```

```java
public class Artillero extends Soldado {
    private int numCohetes;

    public Artillero(String nombre, int numCohetes) {
        super(nombre);
        this.numCohetes = numCohetes;
    }

    public void informar() {
        // System.out.println(nombre); // ERROR: nombre es private en Soldado
        System.out.println("Artillero: " + getNombre() + ", cohetes: " + numCohetes);
    }
}
```

---

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

### Respuesta

La compatibilidad de tipos mejora mucho la **extensibilidad** del código. Cuando se programa contra el tipo general, en este caso `Soldado`, pueden añadirse nuevas subclases sin tener que reescribir el código que ya trabaja con soldados en general. Eso permite ampliar el sistema con nuevos tipos concretos manteniendo intacta la lógica común.

Por ejemplo, si más adelante se añade un `Francotirador`, seguirá siendo compatible con `Soldado`. Por tanto, podrá introducirse en el mismo array y el recorrido que pide el saludo seguirá funcionando sin cambios. Esa es una de las ventajas más importantes de la herencia bien utilizada: el código cliente depende del tipo general y no necesita conocer todos los subtipos concretos.

```java
public class Francotirador extends Soldado {
    private int numBalas;

    public Francotirador(String nombre, int numBalas) {
        super(nombre);
        this.numBalas = numBalas;
    }

    public int getNumBalas() {
        return numBalas;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Soldado[] ejercito = new Soldado[5];
        ejercito[0] = new Artillero("Luis", 5);
        ejercito[1] = new Zapador("Marta", 3);
        ejercito[2] = new Francotirador("Raúl", 20);
        ejercito[3] = new Artillero("Carlos", 8);
        ejercito[4] = new Zapador("Elena", 2);

        for (Soldado s : ejercito) {
            s.saludar(); // este código no cambia
        }
    }
}
```

---

## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

### Respuesta

Sí, en Java puede tenerse una referencia del supertipo apuntando a un objeto real de un subtipo. Por ejemplo, una variable de tipo `Soldado` puede referirse a un objeto `Artillero`. Eso es completamente válido y es la base de la compatibilidad de tipos. Sin embargo, con esa referencia solo pueden invocarse directamente los métodos visibles en el tipo declarado de la variable, es decir, los métodos de `Soldado` y no los específicos de `Artillero`.

El **upcasting** consiste en tratar un objeto de una subclase como si fuera de la superclase. Es implícito y seguro, por ejemplo al guardar un `Artillero` en una variable `Soldado`. El **downcasting** es la operación inversa: convertir una referencia del supertipo a una referencia de un subtipo concreto. Esa conversión debe hacerse explícitamente y solo es válida si el objeto real pertenece a ese subtipo. Para comprobarlo se usa `instanceof`, que permite saber en tiempo de ejecución si una referencia apunta realmente a un objeto de una clase determinada.

```java
public class Main {
    public static void main(String[] args) {
        Soldado[] ejercito = new Soldado[4];
        ejercito[0] = new Artillero("Luis", 5);
        ejercito[1] = new Zapador("Marta", 3);
        ejercito[2] = new Artillero("Carlos", 8);
        ejercito[3] = new Zapador("Elena", 2);

        for (Soldado s : ejercito) {
            s.saludar();

            if (s instanceof Artillero) {
                Artillero a = (Artillero) s; // downcasting
                System.out.println("Cohetes disponibles: " + a.getNumCohetes());
            }
        }
    }
}
```

---

## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

### Respuesta

El acceso **protegido** (`protected`) permite que un atributo o método no sea completamente público, pero sí accesible desde las subclases. Es una visibilidad intermedia entre `private` y `public`. En Java se implementa escribiendo la palabra reservada `protected` delante del atributo o del método.

Esto resulta útil cuando se quiere permitir a las subclases trabajar con parte del estado heredado sin abrirlo a todo el mundo. Aun así, debe usarse con cuidado, porque expone más detalles internos que `private`. En este ejemplo, si `nombre` se declara como `protected`, la clase `Zapador` podrá usarlo directamente dentro de su método para poner minas o bombas.

```java
public class Soldado {
    protected String nombre;

    public Soldado(String nombre) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("El nombre no puede estar vacío");
        }
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Hola, soy " + nombre);
    }
}
```

```java
public class Zapador extends Soldado {
    private int numMinas;

    public Zapador(String nombre, int numMinas) {
        super(nombre);
        this.numMinas = numMinas;
    }

    public int getNumMinas() {
        return numMinas;
    }

    public void ponerBomba() {
        if (numMinas > 0) {
            System.out.println(nombre + " ha colocado una mina");
            numMinas--;
        } else {
            System.out.println(nombre + " no tiene minas");
        }
    }
}
```

---

## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

### Respuesta

No en todos los lenguajes orientados a objetos existe obligatoriamente una única clase base universal para todos los objetos. Depende del diseño del lenguaje. Hay lenguajes en los que esa raíz común sí existe de forma clara y otros en los que el modelo es diferente o más flexible.

En Java sí ocurre: toda clase hereda directa o indirectamente de `Object`. Aunque no se escriba `extends Object`, esa herencia existe de forma implícita. Por eso, todos los objetos en Java comparten ciertos métodos básicos heredados de `Object`, como `toString()`, `equals()` o `hashCode()`. Esto no se aplica a los tipos primitivos como `int` o `double`, porque no son objetos.

---

## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

### Respuesta

La **herencia múltiple** consiste en que una clase herede de más de una clase base al mismo tiempo. Eso permitiría recibir estado y comportamiento desde varias superclases distintas. Conceptualmente parece útil, pero puede generar conflictos, por ejemplo si dos clases base tienen métodos con el mismo nombre o comportamientos incompatibles.

En Java **no existe herencia múltiple de clases**. Una clase solo puede extender a una única superclase. Lo que sí permite Java es implementar varias interfaces, lo que da cierta flexibilidad sin introducir todos los problemas de la herencia múltiple de implementación. Por tanto, en Java puede hablarse de “múltiples tipos” a través de interfaces, pero no de múltiples superclases reales.

---

## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente.

### Respuesta

En Java, una excepción personalizada puede definirse como cualquier otra clase, pero heredando de una clase de excepciones. Si se quiere que sea **no controlada**, debe heredarse de `RuntimeException`. Eso significa que no obliga a declarar `throws` ni a capturarla obligatoriamente. Además, como las excepciones son objetos, pueden componerse con otros objetos útiles para describir mejor el error.

En este caso, la excepción `UsuarioNoEncontradoException` contendrá un `Usuario`, de modo que el objeto problemático quede almacenado dentro de la excepción. También conviene ofrecer dos constructores: uno normal y otro que permita incluir una causa subyacente. Así se sigue el mismo patrón que muchas excepciones estándar de Java, conservando información más completa sobre el fallo.

```java
public class Usuario {
    private final String login;

    public Usuario(String login) {
        if (login == null || login.isBlank()) {
            throw new IllegalArgumentException("El login no puede estar vacío");
        }
        this.login = login;
    }

    public String getLogin() {
        return login;
    }

    @Override
    public String toString() {
        return "Usuario{" + "login='" + login + '\'' + '}';
    }
}
```

```java
public class UsuarioNoEncontradoException extends RuntimeException {
    private final Usuario usuario;

    public UsuarioNoEncontradoException(Usuario usuario) {
        super("Usuario no encontrado: " + usuario);
        this.usuario = usuario;
    }

    public UsuarioNoEncontradoException(Usuario usuario, Throwable causa) {
        super("Usuario no encontrado: " + usuario, causa);
        this.usuario = usuario;
    }

    public Usuario getUsuario() {
        return usuario;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Usuario u = new Usuario("alejandro");

        throw new UsuarioNoEncontradoException(u);

        // Ejemplo con causa:
        // throw new UsuarioNoEncontradoException(u, new IllegalStateException("Fallo al acceder a la base de datos"));
    }
}
```

---

## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

### Respuesta

No debe usarse herencia únicamente para reutilizar código porque la herencia no expresa solo reutilización, sino una relación conceptual fuerte de tipo **“es-un”**. Si esa relación no existe realmente, el diseño queda forzado y confuso. Una clase puede terminar heredando métodos o atributos que no le corresponden solo por aprovechar código ya escrito.

Además, la herencia acopla mucho la subclase con la superclase. La subclase depende de cómo está diseñada la clase base y de sus futuras modificaciones. Si lo único que se busca es reaprovechar funcionalidad, suele ser preferible usar composición: una clase contiene o utiliza a otra y delega en ella la tarea necesaria. Así se obtiene reutilización con menos rigidez y con una relación conceptual más limpia.

---

## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

### Respuesta

Se recomienda favorecer la composición porque suele producir diseños más flexibles. Con composición, una clase utiliza otras como partes internas o colaboradoras, sin quedar tan atada a una jerarquía rígida. Eso permite cambiar componentes, sustituir comportamientos o reorganizar responsabilidades con menos impacto en el resto del sistema.

La herencia, en cambio, crea una dependencia más fuerte entre subclase y superclase. Si la superclase cambia, las subclases pueden verse afectadas. Con composición, el acoplamiento suele ser menor y la encapsulación se conserva mejor. Por ello, cuando no exista una relación clara de “es-un”, suele resultar más adecuado componer objetos que heredar de ellos.

---

## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

### Respuesta

Cuando se dice que la herencia rompe la encapsulación, se hace referencia a que la subclase pasa a depender en parte de detalles internos de la superclase. Aunque la superclase oculte parte de su estado, la subclase necesita conocer cómo se comporta, qué métodos ofrece para extenderse y qué expectativas internas tiene. Esa dependencia hace que el interior de la clase base influya directamente en las clases hijas.

El problema aparece sobre todo cuando una subclase se apoya en detalles de implementación de la superclase, especialmente mediante miembros `protected` o comportamientos no pensados para ser alterados. Entonces, un cambio interno en la clase base puede romper subclases que parecían correctas. En ese sentido, la herencia expone más de la estructura interna de una clase de lo que ocurriría con una simple composición.

---

## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

### Respuesta

Con herencia, puede modelarse la parte común en una superclase `Persona`, que contenga el `dni` y el `nombre`. Después, `Estudiante` y `Trabajador` heredan esos datos y añaden solo su parte específica. Este enfoque es adecuado cuando realmente puede afirmarse que un estudiante es-una persona y un trabajador es-una persona, lo cual conceptualmente tiene sentido.

Con composición, en lugar de heredar, se crea una clase `DatosPersonales` y tanto `Estudiante` como `Trabajador` la contienen como atributo. En este modelo, ambas clases **tienen** datos personales, en vez de **ser** una `Persona` dentro de la jerarquía. Esta solución suele ser más flexible cuando interesa reutilizar datos comunes sin acoplar todas las clases a una única superclase.

```java
// Modelo por herencia
public class Persona {
    private String dni;
    private String nombre;

    public Persona(String dni, String nombre) {
        if (dni == null || dni.isBlank() || nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("DNI y nombre son obligatorios");
        }
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() {
        return dni;
    }

    public String getNombre() {
        return nombre;
    }
}
```

```java
public class Estudiante extends Persona {
    private String titulacion;

    public Estudiante(String dni, String nombre, String titulacion) {
        super(dni, nombre);
        this.titulacion = titulacion;
    }

    public String getTitulacion() {
        return titulacion;
    }
}
```

```java
public class Trabajador extends Persona {
    private String empresa;

    public Trabajador(String dni, String nombre, String empresa) {
        super(dni, nombre);
        this.empresa = empresa;
    }

    public String getEmpresa() {
        return empresa;
    }
}
```

```java
// Modelo por composición
public class DatosPersonales {
    private String dni;
    private String nombre;

    public DatosPersonales(String dni, String nombre) {
        if (dni == null || dni.isBlank() || nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("DNI y nombre son obligatorios");
        }
        this.dni = dni;
        this.nombre = nombre;
    }

    public String getDni() {
        return dni;
    }

    public String getNombre() {
        return nombre;
    }
}
```

```java
public class EstudianteComp {
    private DatosPersonales datos;
    private String titulacion;

    public EstudianteComp(DatosPersonales datos, String titulacion) {
        if (datos == null) {
            throw new IllegalArgumentException("Los datos personales son obligatorios");
        }
        this.datos = datos;
        this.titulacion = titulacion;
    }

    public DatosPersonales getDatos() {
        return datos;
    }

    public String getTitulacion() {
        return titulacion;
    }
}
```

```java
public class TrabajadorComp {
    private DatosPersonales datos;
    private String empresa;

    public TrabajadorComp(DatosPersonales datos, String empresa) {
        if (datos == null) {
            throw new IllegalArgumentException("Los datos personales son obligatorios");
        }
        this.datos = datos;
        this.empresa = empresa;
    }

    public DatosPersonales getDatos() {
        return datos;
    }

    public String getEmpresa() {
        return empresa;
    }
}
```

Si se desea un criterio práctico, la herencia conviene cuando se quiere tratar a `Estudiante` y `Trabajador` como tipos de una familia común `Persona`. La composición conviene cuando se quiere reutilizar la información personal sin crear una jerarquía fuerte. Ambas soluciones son válidas, pero la composición suele resultar más fácil de mantener cuando lo común es solo un conjunto de datos compartidos.
