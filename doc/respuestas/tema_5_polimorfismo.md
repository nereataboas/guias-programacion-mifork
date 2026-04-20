Aquí se presentan las respuestas relativas al concepto de polimorfismo, adaptadas a una perspectiva comparativa con C/C++ procedimental y redactadas en formato impersonal, respetando las restricciones de extensión.

# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

### Respuesta
El **polimorfismo** (del griego "múltiples formas") es la capacidad de los lenguajes orientados a objetos para tratar objetos de distintas clases derivadas de manera uniforme, utilizando una referencia o puntero a su clase base común. Sirve primordialmente para escribir código genérico y altamente extensible. En programación procedimental (como en C puro), operar con una colección heterogénea exigiría el uso de uniones (`union`), enumeraciones de tipos y extensas sentencias `switch` para determinar qué función llamar para cada tipo. El polimorfismo elimina esta burocracia delegando la decisión al propio objeto.

La **sobreescritura** (o *overriding*) es el mecanismo fundamental que hace posible el polimorfismo de comportamiento. Consiste en redefinir en una subclase la implementación completa de un método que ya había sido definido en la superclase. La nueva versión reemplaza el comportamiento original para los objetos de la subclase, asegurando que la acción realizada sea la específica y correcta para esa especialización.

## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

### Respuesta
La **ligadura dinámica** (o *late binding*) es el mecanismo de resolución de llamadas a funciones donde la dirección de memoria de la rutina a ejecutar no se decide durante el proceso de compilación, sino en tiempo de ejecución. La decisión se toma examinando el tipo real del objeto instanciado en el Heap, independientemente del tipo del puntero o referencia a través del cual se le invoca. Constituye el motor técnico subyacente que permite que el polimorfismo funcione.

Su implementación y activación explícita varían radicalmente según el lenguaje. En **Java**, la ligadura dinámica es el comportamiento estándar y por defecto para todos los métodos de instancia (que no sean privados, estáticos o finales). No se precisa ninguna directiva especial en el código fuente para que las llamadas se resuelvan polimórficamente.

En **C++**, por el contrario, la filosofía del lenguaje prioriza el rendimiento, por lo que la ligadura es estática por defecto (resuelta en compilación). Para lograr polimorfismo en C++, el programador está obligado a indicarlo de forma explícita, marcando los métodos correspondientes de la clase base con la palabra reservada `virtual`, forzando así la creación de una tabla de funciones virtuales (vtable) en memoria.

En el caso de **Python**, al ser un lenguaje interpretado y de tipado dinámico, no existe el concepto de ligadura estática. Todo el enlace de funciones es tardío y depende exclusivamente del objeto evaluado en tiempo de ejecución, lo que proporciona un polimorfismo natural e inherente (conocido como *duck typing*) sin necesidad de herencias estrictas ni palabras clave.

## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

### Respuesta
Se presenta a continuación el diseño solicitado. Al recorrer el array `peloton`, el código iterador utiliza exclusivamente referencias del tipo genérico `Soldado`. Sin embargo, gracias a la ligadura dinámica discutida anteriormente, la Máquina Virtual de Java detecta el tipo real instanciado y deriva la ejecución de la orden `saludar()` a la implementación específica de cada clase en particular.

```java
class Soldado {
    protected String nombre;

    public Soldado(String nombre) {
        this.nombre = nombre;
    }

    public void saludar() {
        System.out.println("Soldado " + nombre + " presentándose.");
    }
}

class Zapador extends Soldado {
    public Zapador(String nombre) {
        super(nombre);
    }

    // Sobreescritura total del método
    @Override
    public void saludar() {
        System.out.println("ZAPADOR " + nombre + " LISTO PARA DEMOLICIÓN.");
    }
}

class Artillero extends Soldado {
    public Artillero(String nombre) {
        super(nombre);
    }
    // El Artillero decide no sobreescribir; hereda el saludo base intacto.
}

public class DemostracionPolimorfismo {
    public static void main(String[] args) {
        // Estructura genérica que aloja múltiples tipos (Upcasting)
        Soldado[] peloton = new Soldado[3];
        peloton[0] = new Soldado("Recluta A");
        peloton[1] = new Zapador("MacGyver");
        peloton[2] = new Artillero("Rambo");

        // Llamada polimórfica sin estructuras 'switch' ni 'if'
        for (int i = 0; i < peloton.length; i++) {
            peloton[i].saludar();
        }
    }
}
```

## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

### Respuesta
Sí, es completamente posible e idóneo invocar la implementación original del método desde el interior de su versión sobreescrita. Esta técnica se emplea asiduamente para extender el comportamiento heredado, reutilizando la lógica base en lugar de duplicar código innecesariamente en la subclase.

Para lograr esta invocación sin caer en una recursión infinita (lo que sucedería si solo se llamara a `saludar()` a secas), se utiliza la palabra clave `super`. Funciona de manera similar a su uso en constructores, pero en este contexto se combina con la sintaxis de acceso a métodos (`super.nombreDelMetodo()`), dirigiendo la llamada directamente a la tabla de funciones de la superclase.

```java
class Zapador extends Soldado {
    public Zapador(String nombre) {
        super(nombre);
    }

    @Override
    public void saludar() {
        // 1. Reutiliza el comportamiento de la clase base usando 'super'
        super.saludar();
        // 2. Extiende el comportamiento con su lógica específica
        System.out.println("¡ZAPADOR A SUS ORDENES!");
    }
}
```

## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

### Respuesta
Para que la sobreescritura sea válida, existen restricciones sintácticas severas: la firma del método (su nombre y la secuencia exacta de los tipos de sus parámetros) debe ser matemáticamente idéntica. El tipo de retorno debe coincidir o ser una subclase estricta del retorno original (tipo de retorno covariante). Asimismo, el nivel de visibilidad no puede volverse más restrictivo (un método `public` no puede sobreescribirse como `protected` o `private`).

La **sobreescritura (*overriding*)** altera la implementación de un método para responder al polimorfismo y se evalúa en tiempo de ejecución. Por contra, la **sobrecarga (*overloading*)** es la creación de múltiples funciones bajo un mismo nombre pero con parámetros distintos dentro de la misma clase. La sobrecarga no tiene relación con el polimorfismo dinámico; el compilador decide qué versión ejecutar durante la compilación en base a los argumentos suministrados (ligadura estática).

La anotación `@Override` es un metadato insertado justo encima de un método para instruir al compilador a que verifique estrictamente que se está sobreescribiendo una función de la superclase. Es sumamente recomendable utilizarla siempre porque previene fallos graves por errores tipográficos: si el programador escribe mal el nombre (ej. `saludr()` en lugar de `saludar()`), sin la anotación el compilador asumiría que es una función nueva; con `@Override`, el compilador lanza un error inmediato advirtiendo que no existe tal método en la clase padre.

## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

### Respuesta
Efectivamente, en el ecosistema de Java el polimorfismo es una herramienta presente e ineludible desde las primeras etapas de aprendizaje, operando de manera invisible incluso antes de que se defina la primera jerarquía de herencia personalizada. Esto se debe al diseño central del lenguaje, donde todas las clases descienden inexorablemente de la clase matriz `java.lang.Object`.

Al redefinir métodos como `toString()` o `equals()` en una clase propia para que devuelvan formatos de texto útiles o comparen atributos específicos, se está ejecutando una sobreescritura clásica. 

Cuando funciones genéricas del lenguaje, como la instrucción para imprimir por pantalla `System.out.println(cualquierObjeto)`, procesan la orden, interactúan con la referencia tratándola como un `Object` y llaman a su función `toString()`. Gracias a la ligadura dinámica y al polimorfismo implícito, se ejecuta la versión del método que el programador ha diseñado para esa entidad específica, en lugar del identificador de memoria genérico que aporta la superclase originaria.

## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

### Respuesta
Una **clase abstracta** es un molde estructural incompleto que sirve exclusivamente como cimiento genérico para otras subclases, definiendo interfaces o partes comunes pero dejando detalles clave sin resolver. Un **método abstracto** es justamente ese detalle sin resolver: declara su firma y parámetros pero prescinde por completo de cuerpo (se cierra con punto y coma en lugar de llaves), obligando arquitectónicamente a cualquier subclase no abstracta a sobreescribirlo y proveer el código final.

Debido a su naturaleza inconclusa, es absolutamente imposible crear instancias de una clase abstracta. El compilador bloquea cualquier intento de usar el operador `new` sobre ella para garantizar que nunca se pueda invocar un método que carezca de implementación ejecutable en memoria.

La directiva `abstract` se debe ubicar tanto en la rúbrica de la clase como en la declaración individual del método inacabado.

```java
// Se marca la clase como incompleta
abstract class Soldado {
    protected String nombre;

    public Soldado(String nombre) { this.nombre = nombre; }

    public void saludar() {
        System.out.println("Soldado " + nombre + " reportándose.");
    }

    // Método abstracto: obliga a las subclases a aportar la lógica
    public abstract void atacar();
}

class Artillero extends Soldado {
    public Artillero(String nombre) { super(nombre); }

    // Implementación obligatoria del método heredado
    @Override
    public void atacar() {
        System.out.println(nombre + " dispara el cañón de artillería.");
    }
}
```

## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

### Respuesta
La palabra clave `final` actúa como una directiva de cierre estructural en Java. Si se aplica sobre la declaración de una clase (ej. `public final class Documento`), el compilador deniega rotundamente la posibilidad de que otras clases puedan heredar de ella. Si se ubica en la declaración de un método dentro de una clase estándar, permite que la clase sea heredada, pero prohíbe de forma terminante la sobreescritura de dicho método específico en cualquier nivel de la jerarquía inferior.

Su relación con el polimorfismo es antagónica: el uso de `final` desactiva deliberadamente el polimorfismo de comportamiento para blindar el código. El arquitecto de software emplea esta restricción para certificar que una subrutina crítica, ligada a la seguridad o invariantes inamovibles, conservará siempre su lógica original sin temor a modificaciones imprevistas y potencialmente defectuosas introducidas por subclases desarrolladas por terceros.

El ejemplo más representativo en la API estándar de Java es la clase `String`. Está diseñada herméticamente como `final` para garantizar matemáticamente su inmutabilidad. Si no fuera `final`, un código hostil podría crear una subclase engañosa de cadena de texto (`MaliciousString`) que alterase subrepticiamente sus caracteres tras haber superado las validaciones de seguridad de un entorno de red o acceso a bases de datos.

## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

### Respuesta
Las **interfaces** en Java definen un contrato formal de comportamiento puro desprovisto, históricamente, de cualquier tipo de estado o implementación. En su concepción más estricta, una interfaz expone exclusivamente constantes públicas y firmas de métodos totalmente abstractos que otra clase asume la responsabilidad de materializar.

Aunque su funcionamiento asemeja al de las clases abstractas, su función arquitectónica difiere drásticamente. Las clases abstractas consolidan una taxonomía ("es un", como "Un Águila es un Animal"), mientras que las interfaces imponen roles, capacidades o transacciones ("es capaz de", como "Un Águila implementa la capacidad de Volar"). Esto permite dotar de polimorfismo compartido a objetos dispares que no encajan en el mismo árbol genealógico (un Ave y un Avión pueden implementar la interfaz `Volador`).

A diferencia de la restricción de herencia simple de las clases (donde solo se permite la instrucción `extends` hacia una única superclase), Java posibilita que una clase concrete tantas interfaces como sea necesario simultáneamente (ej. `class Archivo implements Leible, Borrable, Copiable`). Este mecanismo proporciona las ventajas del diseño modular sin los conflictos fatales típicos de la herencia múltiple estructural de C++ ("Problema del Diamante").

## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

### Respuesta
En este modelo, el polimorfismo se utiliza para abstraer la complejidad geométrica de la entidad contenedora. La clase `Linea` ignora el contexto de dimensionalidad; opera ciegamente a través del contrato general `Punto` y confía en el mecanismo de ligadura dinámica para ejecutar la matemática adecuada sin recurrir a instrucciones de control de flujo manuales.

Dentro de las clases especializadas, se emplean los mecanismos de identificación de tipos en tiempo de ejecución (`instanceof`) para prevenir que se cruce información lógicamente incompatible, como calcular un vector entre un espacio bidimensional y uno tridimensional. Si el objeto coincide, se realiza el *downcasting* pertinente para extraer las coordenadas específicas.

```java
abstract class Punto {
    // Contrato abstracto polimórfico
    public abstract double calcularDistanciaA(Punto otro);
}

class Punto2D extends Punto {
    private double x, y;

    public Punto2D(double x, double y) { this.x = x; this.y = y; }

    @Override
    public double calcularDistanciaA(Punto otro) {
        if (otro instanceof Punto2D) {
            Punto2D p2 = (Punto2D) otro; // Downcasting seguro
            return Math.sqrt(Math.pow(this.x - p2.x, 2) + Math.pow(this.y - p2.y, 2));
        }
        throw new IllegalArgumentException("Incompatible: se esperaba un Punto2D");
    }
}

class Punto3D extends Punto {
    private double x, y, z;

    public Punto3D(double x, double y, double z) { this.x = x; this.y = y; this.z = z; }

    @Override
    public double calcularDistanciaA(Punto otro) {
        if (otro instanceof Punto3D) {
            Punto3D p3 = (Punto3D) otro; // Downcasting seguro
            return Math.sqrt(Math.pow(this.x - p3.x, 2) + Math.pow(this.y - p3.y, 2) + Math.pow(this.z - p3.z, 2));
        }
        throw new IllegalArgumentException("Incompatible: se esperaba un Punto3D");
    }
}

class Linea {
    private Punto p1, p2;

    public Linea(Punto p1, Punto p2) {
        this.p1 = p1;
        this.p2 = p2;
    }

    public double longitud() {
        // Ejecución polimórfica: la Linea desconoce la dimensionalidad
        return p1.calcularDistanciaA(p2);
    }
}
```

## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

### Respuesta
La **herencia de interfaces** es el mecanismo que posibilita construir jerarquías de contratos de forma puramente abstracta. Ocurre cuando una interfaz adopta y amplía las exigencias establecidas por una interfaz previa. En este escenario, la interfaz hija utiliza la palabra clave `extends` (no `implements`) y fuerza a que cualquier clase que decida adoptarla deba proveer la implementación tanto de los métodos de la interfaz base como de los definidos en la extensión.

Sí existe la **herencia múltiple de interfaces** en el diseño del lenguaje Java. Una sola interfaz tiene capacidad técnica para incorporar y fusionar el comportamiento de varias interfaces primarias separando sus nombres con comas (ej. `interface Nueva extends A, B`). Dado que los contratos no conllevan variables de estado conflictivas ni rutinas implementadas, el compilador los asimila sin riesgo de ambigüedad.

```java
// Interfaz base
interface Fichero {
    String leerContenido();
}

// Herencia de interfaz: expande los requisitos del contrato original
interface FicheroEscribible extends Fichero {
    void escribirContenido(String texto);
    void eliminarFichero();
}

// La clase concreta asume forzosamente los 3 métodos en total
class ArchivoTexto implements FicheroEscribible {
    @Override
    public String leerContenido() { return "Datos de texto..."; }

    @Override
    public void escribirContenido(String texto) { System.out.println("Guardando: " + texto); }

    @Override
    public void eliminarFichero() { System.out.println("Archivo borrado de forma segura."); }
}
```