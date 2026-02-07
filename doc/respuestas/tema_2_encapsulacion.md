<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Encapsulación". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 2. Encapsulación

## 1. Encapsulación y ocultación de información

La **encapsulación** busca agrupar en una misma unidad (la clase) los **datos** (atributos) y las **operaciones** (métodos) que trabajan con esos datos. La **ocultación de información** pretende esconder los detalles internos de implementación y obligar a que el acceso se haga mediante una interfaz controlada.

Entre las ventajas típicas de la ocultación están: menor acoplamiento entre clases, posibilidad de cambiar la implementación interna sin romper el código cliente, protección de **invariantes** (evitar estados inválidos), y mejor mantenibilidad al limitar los puntos donde se toca el estado del objeto.

---

## 2. Interfaz pública en POO y relación con la ocultación

La **interfaz pública** de una clase/objeto es el conjunto de miembros accesibles desde fuera (normalmente métodos ```java public`, y si existieran, constructores o campos ```java public`). Se puede entender como el “contrato” que ofrece la clase: qué operaciones se permiten y cómo se usan.

La ocultación de información se apoya en esto porque se expone solo lo necesario como interfaz pública, y se mantiene privada la representación interna (atributos y helpers). Así el exterior depende del contrato, no de cómo esté implementado por dentro.

---

## 3. Diseño cuidadoso de la interfaz pública

La interfaz pública conviene diseñarla con cuidado porque es lo que el resto del programa usará; si se expone demasiado o se expone mal, se crean dependencias innecesarias y se dificulta mantener invariantes. Una API pequeña, coherente y estable facilita el mantenimiento y la evolución.

Cambiar una interfaz pública suele ser costoso porque obliga a modificar todos los sitios donde se usa. En general, añadir métodos nuevos es más fácil que renombrar/eliminar o cambiar la firma de métodos existentes.

---

## 4. Invariantes de clase y ayuda de la ocultación

Las **invariantes de clase** son reglas que deben cumplirse siempre para que un objeto sea válido (por ejemplo, “un valor debe estar en un rango” o “un estado no puede ser nulo”). Son condiciones de consistencia que deben mantenerse tras cualquier operación.

La ocultación ayuda porque evita modificaciones directas desde fuera. Al forzar cambios a través de métodos/constructores, se puede validar y asegurar que el objeto no queda en un estado inválido.

---

## 5. Clase `Punto` con ocultación, interfaz pública, ` public` y `private`

La clase siguiente oculta `x` e `y` con `private` y expone métodos ```java public` para operar con el punto. Esto evita que desde fuera se modifiquen o se dependan de detalles internos.

La **interfaz pública** serían los miembros ` public`: constructor, `getX()`, `getY()` y `calcularDistanciaAOrigen()`. `public` significa accesible desde cualquier clase; `private` significa accesible solo dentro de la propia clase.


```java 
public class Punto {     
    private final double x;     
    private final double y;      
    public Punto(double x, double y) {         
        this.x = x;         
        this.y = y;     
    }      
    public double getX() { return x; }     
    public double getY() { return y; }      
    public double calcularDistanciaAOrigen() {         
        return Math.hypot(x, y);     
    }     
}
```

---

## 6. ¿A quién se aplican `public` o `private` en Java?

En Java se aplican a miembros de clases: campos (atributos), métodos, constructores y tipos anidados (clases internas, enums internos, interfaces internas). Controlan qué se expone y qué queda oculto.

Una clase de nivel superior no puede ser `private`; solo puede ser `public` o sin modificador (visibilidad de paquete). Lo “realmente privado” como tipo debe declararse dentro de otra clase.

---

## 7. Más tipos de visibilidad: Java y otros lenguajes

Además de `public` y `private`, Java tiene `protected` y el nivel por defecto (sin modificador), llamado _package-private_. `protected` permite acceso a subclases (y también al mismo paquete), y el nivel por defecto limita el acceso al mismo paquete.

En otros lenguajes hay variantes similares. Por ejemplo, C++ tiene `public/protected/private` por secciones, y C# incorpora `internal` (acceso dentro del ensamblado) y combinaciones. En Python la privacidad es convencional (no estricta) y se basa en nombres.

---

## 8. Privados: ocultos para otras clases o para otras instancias

Un miembro `private` está oculto para **otras clases**, pero no está oculto para **otras instancias** si se accede desde dentro del código de la misma clase. La regla es “privado a nivel de clase”, no “privado a nivel de objeto”.

Por eso un método de `Punto` puede leer `otro.x` aunque `x` sea `private`, porque el acceso ocurre dentro de `Punto`.

```java
public class Punto {
    private final double x;     
    private final double y;      
    public Punto(double x, double y) {         
        this.x = x;         
        this.y = y;     
    }      
    public double calcularDistanciaAPunto(Punto otro) {         
        return Math.hypot(this.x - otro.x, this.y - otro.y);     
        } 
}
```

---

## 9. ¿Qué son getters y setters?

Un **getter** es un método para leer un atributo (por ejemplo, `getX()`), y un **setter** es un método para modificarlo (por ejemplo, `setX(valor)`). Su papel es dar acceso controlado al estado interno.

Permiten validar datos, mantener invariantes y cambiar la implementación interna sin tocar el código cliente, siempre que la interfaz pública se mantenga estable.

---

## 10. “Seguridad” por ocultación: ¿anti-hack?

Cuando se dice que la ocultación mejora la “seguridad”, normalmente se refiere a robustez y corrección: impedir modificaciones accidentales, reducir errores y evitar estados inválidos.

No significa, por sí sola, que un programa no pueda ser “hackeado” en términos de ciberseguridad. Aun así, una interfaz más pequeña y controlada reduce el uso indebido y puede disminuir problemas.

---

## 11. Miembro de instancia vs miembro de clase y ocultación

Un **miembro de instancia** pertenece a cada objeto: cada instancia tiene sus propios valores (como `x` e `y`). Un **miembro de clase** pertenece a la clase y se comparte entre todas las instancias (en Java se declara con `static`).

Los miembros de clase también se pueden ocultar: un `static` puede ser `private` y exponerse, si interesa, mediante métodos `public static` de consulta/control.

---

## 12. ¿Tiene sentido constructores privados?

Sí. Un constructor `private` impide crear objetos desde fuera, y se usa para controlar la creación: patrones como **Singleton**, métodos factoría, cacheo de instancias o clases de utilidades (solo `static`).

También aparece en `enum` (constructores privados de forma natural) para garantizar que solo existan las instancias declaradas.

---

## 13. Miembros de clase en Java y máximos `x` e `y`

En Java los miembros de clase se indican con `static`. Para registrar máximos globales de `x` e `y`, se guardan en campos `static` y se actualizan en el constructor cuando se crea cada punto.

Se suelen exponer con getters `public static` y mantener los campos como `private static` para que nadie los cambie desde fuera.

```java 
public class Punto {     
    private final double x;     
    private final double y;      
    private static double maxX = Double.NEGATIVE_INFINITY;     
    private static double maxY = Double.NEGATIVE_INFINITY;      
    public Punto(double x, double y) {         
        this.x = x;         
        this.y = y;         
        if (x > maxX) maxX = x;         
        if (y > maxY) maxY = y;     
    }      
    public static double getMaxX() { return maxX; }     
    public static double getMaxY() { return maxY; } 
}
```

---

## 14. Método factoría que redondea (solo código) y uso de `static`

Un método factoría crea objetos aplicando una lógica centralizada (como redondear). Se usa `static` porque se quiere invocar sin tener una instancia previa del objeto que se va a construir.

```java 
public static Punto crearRedondeado(double x, double y) {     
    double rx = Math.round(x);     
    double ry = Math.round(y);     
    return new Punto(rx, ry); 
}
```

---

## 15. Implementación con array interno sin cambiar interfaz pública

Gracias a la ocultación, se puede cambiar la representación interna a un `double[]` sin modificar la interfaz pública (mismos métodos públicos). El código cliente sigue llamando a `getX()`, `getY()` y distancias igual que antes.

Esto ilustra que el exterior debe depender del contrato público, no de la estructura interna de datos.

```java 
public class Punto {     
    private final double[] coords = new double[2];      
    public Punto(double x, double y) {         
        coords[0] = x;         
        coords[1] = y;     
    }      
    public double getX() { return coords[0]; }     
    public double getY() { return coords[1]; }   
       
    public double calcularDistanciaAOrigen() {         
        return Math.hypot(coords[0], coords[1]);     
    } 
}
```

---

## 16. ¿Mejor atributo público si hay getter y setter?

Aunque haya getter/setter públicos, sigue siendo preferible no exponer el atributo como `public`. Los métodos permiten añadir validaciones, mantener invariantes y cambiar la implementación interna sin afectar al cliente.

La convención habitual en Java es atributos **privados**. Esto se relaciona con invariantes: un campo público se puede cambiar a valores inválidos desde cualquier parte, rompiendo la consistencia del objeto.

---

## 17. Clase inmutable, método modificador y ventajas

Una clase es **inmutable** si su estado no cambia tras crearse (campos `final` y ausencia de métodos que modifiquen el estado). Un **método modificador** es cualquier método que cambia el estado interno del objeto.

Un modificador no tiene por qué ser un “setter”: puede ser algo como `mover(dx, dy)` que cambia varias cosas a la vez. La inmutabilidad aporta menos bugs, objetos más fáciles de razonar y mejor comportamiento en concurrencia.

---

## 18. ¿Conviene poner setters siempre?

No. Incluir setters por costumbre aumenta la superficie de cambios y facilita romper invariantes. Además, hace que otros módulos dependan de detalles que quizá no deberían tocar.

Suele ser mejor exponer solo los cambios que tengan sentido en el dominio, o incluso preferir inmutabilidad y devolver nuevos objetos en lugar de modificar los existentes.

---

## 19. `String` en Java: mutable/inmutable y concatenaciones

`String` es **inmutable** en Java. Al concatenar (con `+`) se crea un nuevo `String`, y los anteriores no se modifican.

Si se van a concatenar muchas veces para construir una cadena larga, conviene usar `StringBuilder` con `append()` y al final `toString()`, evitando muchas cadenas intermedias.

---

## 20. Comparación de objetos, identidad vs contenido y `equals`

En Java `==` compara **identidad** (misma referencia). Para comparar por **contenido**, se usa `equals()` si la clase lo define de esa manera.

Por defecto, `equals()` (el de `Object`) se comporta como identidad. En `String`, la comparación correcta de contenido es `a.equals(b)` (o `Objects.equals(a,b)` si puede haber `null`), no `a == b`.

---

## 21. Wrappers: qué son, cómo se hace y ventajas

Los **wrappers** envuelven tipos primitivos en objetos: `int`→`Integer`, `double`→`Double`, etc. Se puede hacer de forma explícita (p. ej. `Integer.valueOf(5)`) y Java puede hacerlo automáticamente: **autoboxing** y **unboxing**.

Son útiles para colecciones genéricas (`List<Integer>`), para permitir `null` como ausencia de valor y para disponer de utilidades (parseo, constantes, comparaciones). No todos los lenguajes OO necesitan wrappers: en Python los números ya son objetos, y en C# el modelo es distinto (tipos valor y boxing cuando se tratan como `object`).

---

## 22. Enumerados, `enum` como clase y encapsulación

Un **enumerado** define un conjunto finito de valores posibles (meses, estados, etc.). En Java un `enum` se comporta como una clase: puede tener campos, métodos y un constructor.

En encapsulación, aporta seguridad porque solo existen las instancias declaradas, y se puede asociar comportamiento/datos a cada valor sin recurrir a enteros “mágicos”. Además, el compilador ayuda a evitar combinaciones inválidas.

---

## 23. `enum Mes` con atributos privados, constructor y métodos de estaciones

Se define `Mes` con 12 instancias y atributos privados para el ordinal (1–12) y los días. Se añaden métodos para consultar esos datos y métodos para decidir estación según hemisferio, encapsulando toda la lógica dentro del `enum`.

La inversión de estaciones entre hemisferios se implementa cambiando qué meses se consideran de cada estación según el booleano `esHemisferioNorte`.

```java
public enum Mes {     
	ENERO(1, 31),     
	FEBRERO(2, 28),     
	MARZO(3, 31),     
	ABRIL(4, 30),     
	MAYO(5, 31),     
	JUNIO(6, 30),     
	JULIO(7, 31),     
	AGOSTO(8, 31),     
	SEPTIEMBRE(9, 30),     
	OCTUBRE(10, 31),     
	NOVIEMBRE(11, 30),     
	DICIEMBRE(12, 31);      
	private final int numeroEnAnio;     
	private final int dias;      
	private Mes(int numeroEnAnio, int dias) {         
		this.numeroEnAnio = numeroEnAnio;         
		this.dias = dias;     
	}      
	public int getDias() {         
		return dias;     
	}
	public int getOrdinalEnAnio() {
		return numeroEnAnio;
	}     
	public boolean esDePrimavera(boolean esHemisferioNorte) {        
		if (esHemisferioNorte) {             
			return this == MARZO || this == ABRIL || this == MAYO;         
		} else {             
			return this == SEPTIEMBRE || this == OCTUBRE || this == NOVIEMBRE;         
		}     
	}      
	 public boolean esDeVerano(boolean esHemisferioNorte) {         
		if (esHemisferioNorte) {             
			return this == JUNIO || this == JULIO || this == AGOSTO;        
		} else {
			return this == DICIEMBRE || this == ENERO || this == FEBRERO;
		}     
	 }      
	 public boolean esDeOtonio(boolean esHemisferioNorte) {         
		 if (esHemisferioNorte) {
			 return this == SEPTIEMBRE || this == OCTUBRE || this == NOVIEMBRE;         
		 } else {
			return this == MARZO || this == ABRIL || this == MAYO;
		 }     
	 }      
	 public boolean esDeInvierno(boolean esHemisferioNorte) {         
		if (esHemisferioNorte) {
			return this == DICIEMBRE || this == ENERO || this == FEBRERO;
         } else {
			return this == JUNIO || this == JULIO || this == AGOSTO;
		 }
	}
}
```
