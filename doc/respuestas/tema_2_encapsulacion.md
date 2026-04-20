<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Encapsulación". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
------>
# TEMA 2. Encapsulación

## 1. En Programación Orientada a Objetos (POO), ¿Qué buscan la **encapsulación** y **la ocultación** de información? Enumera brevemente algunas ventajas de la ocultación de información.

### Respuesta

La **encapsulación** busca agrupar en una única entidad lógica (la clase) tanto los datos que definen un estado como las funciones que operan sobre ellos. Por otro lado, la **ocultación de información** es el mecanismo de diseño que restringe el acceso directo a los detalles internos de dicha entidad desde el exterior. Mientras que en C cualquier parte del programa puede modificar directamente los campos de un `struct` si tiene acceso a su posición de memoria, la POO establece una barrera protectora alrededor de los datos.

Las ventajas principales de esta ocultación radican en la robustez y la prevención de errores. Al impedir el acceso directo desde el exterior, se evita que otras partes del programa modifiquen el estado interno de forma imprevista o incorrecta, reduciendo drásticamente los efectos secundarios indeseados.

Además, la ocultación facilita el mantenimiento del código. Permite modificar la implementación interna de una clase en el futuro sin que el resto del programa se vea afectado. Por ejemplo, se podría cambiar internamente el tipo de dato de una variable; si ese dato está oculto y solo se interactúa con él mediante funciones, el código externo que utiliza la clase no necesitará ser reescrito ni recompilado.

## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información.

### Respuesta

La **interfaz pública** de una clase es el conjunto de métodos que se exponen explícitamente para que puedan ser invocados por otras partes del programa. Constituye el "contrato" o manual de uso del objeto, definiendo exactamente qué operaciones se le pueden solicitar sin revelar cómo las lleva a cabo internamente.

En el contexto de la programación en C/C++, la interfaz pública es conceptualmente muy análoga a un archivo de cabecera (`.h`). El archivo de cabecera expone las firmas de las funciones disponibles para otros módulos, mientras que la lógica detallada y el manejo de estructuras internas quedan confinadas y ocultas en el archivo de implementación (`.c` o `.cpp`).

Su relación con la ocultación de información es directa: la interfaz pública actúa como la única compuerta de acceso permitida hacia el estado interno protegido. Al obligar a que toda interacción externa pase exclusivamente por esta interfaz, la clase mantiene el control total sobre cómo y cuándo se consultan o modifican sus datos ocultos.

## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

### Respuesta

Es crucial diseñar la interfaz pública con sumo cuidado porque representa un contrato vinculante con el resto del sistema de software. Una vez que otras clases o módulos han sido programados para invocar los métodos de una interfaz pública, se crea una fuerte dependencia entre el código externo y las firmas de dichos métodos.

Cambiar una interfaz pública no es fácil ni deseable en etapas avanzadas de desarrollo. Modificar la lógica interna y oculta de un método es una operación segura, ya que el impacto queda aislado dentro de la propia clase. Sin embargo, alterar la interfaz pública (cambiando el nombre de un método, su tipo de retorno o sus parámetros) provocará errores de compilación en cadena en todas las partes del código externo que dependían de la versión anterior.

Por este motivo, en el diseño orientado a objetos en Java y otros lenguajes, se recomienda aplicar el principio de exponer la menor cantidad de métodos posibles. Cuanto más reducida, estable y bien pensada sea la interfaz pública, menor será el nivel de acoplamiento, facilitando la evolución del programa a largo plazo.

## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

### Respuesta

Las **invariantes de clase** son condiciones lógicas o reglas de negocio que deben cumplirse en todo momento para que el estado de un objeto se considere válido durante su ciclo de vida. Por ejemplo, en una clase que represente una fracción matemática, una invariante fundamental sería que el denominador nunca puede ser cero.

En un entorno procedimental sin ocultación, garantizar estas invariantes es extremadamente difícil. Cualquier sección del código podría acceder directamente a una estructura y realizar una asignación inválida, corrompiendo el estado del sistema y provocando errores fatales posteriores.

La ocultación de información soluciona este problema al hacer que los datos sean privados. La única forma de modificarlos es a través de métodos controlados que actúan como filtros. Estos métodos pueden incluir validaciones lógicas internas (bloques `if`) antes de aplicar cualquier cambio al estado, rechazando asignaciones inválidas y garantizando así que las invariantes de la clase jamás se rompan.

## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

### Respuesta

En Java, las palabras clave `public` y `private` son modificadores de acceso que determinan la visibilidad de los componentes. `private` significa que el atributo o método solo es accesible desde el interior de las llaves que definen la propia clase. `public`, por el contrario, indica que el elemento puede ser invocado o instanciado desde cualquier otra clase del sistema.

En el siguiente ejemplo, la interfaz pública de la clase `Punto` está compuesta exclusivamente por el constructor `Punto(double, double)` y el método `calcularDistanciaAOrigen()`. Las variables `x` e `y` quedan excluidas de esta interfaz al estar marcadas como privadas, logrando la ocultación de la información.

```java
class Punto {
    // Atributos privados: ocultación de información
    private double x;
    private double y;

    // Interfaz pública: Constructor
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // Interfaz pública: Comportamiento
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(this.x * this.x + this.y * this.y);
    }
}

```

## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

### Respuesta

En Java, estos modificadores de acceso se aplican principalmente a los **atributos** (variables miembro) y a los **métodos** de una clase. Marcar atributos como `private` es la base de la encapsulación para proteger el estado, mientras que marcar métodos como `private` es útil para crear subrutinas auxiliares internas que no deben ser llamadas desde fuera.

También se pueden aplicar a los **constructores**. Si se define un constructor como `private`, se impide explícitamente que otras clases externas puedan instanciar objetos mediante el operador `new`. Esto se utiliza frecuentemente en patrones de diseño donde la propia clase controla cómo y cuándo se crean sus instancias.

Finalmente, también se aplican a las **clases**. Una clase principal (la que coincide con el nombre del archivo `.java`) suele ser `public` o tener visibilidad por defecto. Sin embargo, es posible definir clases anidadas (clases dentro de otras clases) y marcarlas como `private` para que actúen como estructuras de datos exclusivas de la clase contenedora.

## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

### Respuesta

Sí, existen otros niveles de visibilidad pensados para arquitecturas de software más complejas y para el uso de la herencia (cuando una clase deriva de otra). El modificador adicional más estandarizado en la POO es `protected`, el cual permite que un miembro sea privado para el código general, pero accesible de forma directa para las clases "hijas" que extiendan la funcionalidad de la clase base.

En **Java**, existen cuatro niveles de visibilidad: `public`, `private`, `protected` y un cuarto nivel llamado "por defecto" (o *package-private*). Si no se escribe ningún modificador, Java asume este nivel, lo que significa que el elemento será visible únicamente para aquellas clases que se encuentren alojadas dentro del mismo directorio lógico o "paquete".

En **otros lenguajes**, la gestión varía. C++ utiliza estrictamente los bloques `public:`, `private:` y `protected:`, sin el concepto de paquetes de Java. Por otro lado, lenguajes como Python carecen de modificadores estrictos de compilador; basan su ocultación en convenciones, asumiendo que cualquier identificador precedido por un guion bajo (ej. `_variable`) es privado y el programador no debe acceder a él directamente.

## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta.

### Respuesta

La respuesta correcta es la **(a): están ocultos para otras clases**. El control de acceso en lenguajes como Java y C++ se evalúa estrictamente a nivel de la *clase* durante el proceso de compilación, no a nivel del *objeto* individual en la memoria durante la ejecución.

Esto implica que un objeto tiene permisos para leer o modificar los atributos privados de cualquier otro objeto, siempre que ambos pertenezcan exactamente a la **misma clase**. El compilador confía en el código que reside dentro de las llaves de la definición de la clase, sin importar sobre qué instancia específica esté operando.

```java
class Punto {
    private double x;
    private double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double calcularDistanciaAPunto(Punto otro) {
        // 'this' es la instancia actual. 'otro' es una instancia diferente.
        // Aunque 'x' e 'y' son privados, podemos acceder directamente
        // a 'otro.x' y 'otro.y' porque estamos dentro de la clase Punto.
        double dx = this.x - otro.x; 
        double dy = this.y - otro.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

```

## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

### Respuesta

Los métodos **"getter"** (consultores) y **"setter"** (modificadores) son funciones públicas estandarizadas que sirven como intermediarios para leer y escribir, respectivamente, el valor de un atributo privado. En lugar de permitir que el código externo acceda directamente a la memoria de la variable (como se haría en C accediendo a un campo de un `struct`), se obliga a que cualquier interacción pase por estas funciones.

La nomenclatura habitual en Java consiste en anteponer la palabra `get` o `set` al nombre del atributo, capitalizando la primera letra de este. Por ejemplo, para un atributo privado llamado `coordenadaX`, su método de lectura sería `getCoordenadaX()` y su método de escritura sería `setCoordenadaX(double valor)`.

El uso de estos métodos otorga un control granular sobre el estado del objeto. Si solo se implementa un "getter" sin su correspondiente "setter", el atributo se convierte efectivamente en una variable de solo lectura para el mundo exterior. Además, el "setter" permite insertar lógica de validación interna antes de confirmar la asignación, protegiendo así la integridad de los datos.

## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

### Respuesta

No, en el contexto del diseño de software orientado a objetos, el término "seguridad" no se refiere a la ciberseguridad o a la resistencia contra ataques informáticos maliciosos. Se refiere a la **seguridad del estado** o robustez del código frente a errores de programación y efectos secundarios no deseados.

En lenguajes como C, es común que un error en la aritmética de punteros sobrescriba accidentalmente la memoria de una estructura ajena, provocando fallos catastróficos difíciles de rastrear (los temidos "segmentation faults"). La ocultación de información busca prevenir este tipo de corrupciones lógicas aislando el estado interno y forzando que todas las modificaciones pasen por canales controlados y predecibles.

Si un atacante tiene acceso a la memoria de la máquina o utiliza herramientas avanzadas (como la *Reflexión* en Java, que permite inspeccionar y alterar metadatos en tiempo de ejecución), puede saltarse las barreras de los atributos `private`. Por lo tanto, la encapsulación es una herramienta de organización y prevención de errores para los programadores, no un mecanismo de encriptación o defensa contra hackers.

## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

### Respuesta

Un **miembro de instancia** (ya sea atributo o método) pertenece exclusivamente a un objeto concreto creado en la memoria. Cada vez que se utiliza el operador `new` (similar a `malloc` en C), se reserva espacio para una nueva copia independiente de todos los atributos de instancia. Si se modifica el atributo de instancia de un objeto, los demás objetos de la misma clase no se ven afectados.

Por el contrario, un **miembro de clase** pertenece a la definición global de la clase y no a un objeto en particular. Solo existe una única copia de este miembro compartida entre todas las instancias, alojada en una zona de memoria estática. Sería el equivalente a declarar una variable global en C, pero cuyo ámbito de nombres está restringido al interior de la clase.

Los miembros de clase **sí se pueden ocultar** utilizando el modificador `private`. Esto es sumamente útil para mantener contadores internos compartidos (por ejemplo, para saber cuántos objetos de esa clase se han instanciado en total) o para definir constantes internas que no deben ser visibles para el resto del programa, evitando así contaminar el espacio de nombres global.

## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

### Respuesta

Sí, tiene mucho sentido y es una práctica común en el diseño de software avanzado. Aunque lo habitual es que un constructor sea `public` para permitir la creación libre de objetos, marcarlo como `private` bloquea explícitamente el uso del operador `new` desde el exterior de la clase.

Esto se utiliza principalmente en patrones de diseño arquitectónico. Un ejemplo clásico es el patrón *Singleton*, el cual garantiza que solo exista una única instancia de una clase en toda la ejecución del programa (útil para gestores de bases de datos o configuraciones). Al hacer el constructor privado, la propia clase asume el control total sobre cuándo y cómo se crea esa única instancia.

Otro caso de uso frecuente son las **clases de utilidad**, como la clase `Math` en Java. Estas clases solo contienen métodos matemáticos estáticos (miembros de clase) y no tienen un estado propio. Como no tiene sentido crear un objeto de tipo "Matemática", su constructor se declara privado para evitar que un programador lo instancie por error.

## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.

### Respuesta

En Java, los miembros de clase se indican añadiendo la palabra reservada `static` en su declaración, justo después del modificador de visibilidad. Para acceder a ellos desde el exterior (si son públicos), se utiliza el nombre de la clase seguido de un punto, en lugar de utilizar el nombre de una variable de instancia (ej. `Punto.maxX`).

A continuación se muestra cómo se implementarían dos atributos compartidos para registrar las coordenadas máximas históricas. Cada vez que el constructor crea un nuevo punto, compara sus coordenadas con las máximas registradas y las actualiza si es necesario.

```java
class Punto {
    private double x;
    private double y;
    
    // Miembros de clase (compartidos por todas las instancias)
    private static double maxX = Double.MIN_VALUE;
    private static double maxY = Double.MIN_VALUE;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
        
        // Actualizamos los miembros de clase si encontramos un nuevo máximo
        if (x > maxX) {
            maxX = x;
        }
        if (y > maxY) {
            maxY = y;
        }
    }
    
    // Getter estático para consultar el valor máximo histórico de X
    public static double getMaxX() {
        return maxX;
    }
}

```

## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`?

### Respuesta

Sí, es estrictamente necesario utilizar `static` en un método factoría. Como el objetivo de este método es precisamente fabricar y devolver un nuevo objeto de tipo `Punto`, debe poder ser invocado *antes* de que exista cualquier instancia previa. Al ser estático, se puede llamar globalmente mediante `Punto.crearPuntoRedondeado(3.4, 5.8)`.

Este método procesa los datos de entrada, aplica la lógica deseada (el redondeo utilizando la librería estándar matemática) y finalmente invoca al constructor real de la clase, delegando en él la creación de la instancia en la memoria Heap.

```java
    // Método factoría estático
    public static Punto crearPuntoRedondeado(double x, double y) {
        double xRedondeado = Math.round(x);
        double yRedondeado = Math.round(y);
        
        // Se llama al constructor normal internamente
        return new Punto(xRedondeado, yRedondeado);
    }

```

## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.

### Respuesta

Este ejercicio demuestra el verdadero poder de la encapsulación y la ocultación de información. Al mantener la interfaz pública intacta (el mismo constructor y el mismo método para calcular la distancia), el código externo que ya utilizaba la clase `Punto` no notará absolutamente ningún cambio ni requerirá ser reescrito, a pesar de que la estructura de datos interna se ha modificado por completo.

En la implementación en C, un cambio similar en un `struct` (pasar de dos variables sueltas a un array) obligaría a buscar y modificar todos los lugares del programa donde se accedía a `p->x` para cambiarlo por `p->coords[0]`. En Java, la barrera protectora absorbe el impacto del cambio.

```java
class Punto {
    // El estado interno ahora es un array. Sigue oculto.
    private double[] coordenadas;

    // La firma del constructor (interfaz pública) NO cambia
    public Punto(double x, double y) {
        // Inicializamos el array en el Heap
        this.coordenadas = new double[2];
        this.coordenadas[0] = x;
        this.coordenadas[1] = y;
    }

    // La firma del método (interfaz pública) NO cambia
    public double calcularDistanciaAOrigen() {
        double x = this.coordenadas[0];
        double y = this.coordenadas[1];
        return Math.sqrt(x * x + y * y);
    }
}

```

## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

### Respuesta

A primera vista, un atributo público o un atributo privado con "getter" y "setter" que no hacen nada más que asignar y devolver el valor parecen funcionalmente idénticos. Sin embargo, no declararlo público es una decisión de diseño orientada al futuro. Si se declara público y el código externo accede a él directamente, cualquier necesidad futura de cambiar su tipo de dato o añadir validaciones romperá todo el programa. Si se usan métodos desde el principio, se asegura la compatibilidad binaria a largo plazo.

La convención universal y más estricta en lenguajes como Java es que **absolutamente todos los atributos deben ser privados** (o protegidos en ciertos casos de herencia). Solo deben exponerse a través de "getters" y "setters" aquellos campos que estrictamente lo requieran, y no es obligatorio crear estos métodos de forma automática para cada variable.

Esto está íntimamente ligado a las "invariantes de clase". Un atributo público permite que cualquier código asigne un valor sin restricciones, rompiendo fácilmente las reglas lógicas (por ejemplo, asignar un valor negativo a un atributo que represente una edad). Al utilizar un "setter", el programador se reserva el derecho de insertar sentencias `if` dentro del método en cualquier momento del ciclo de vida del software, garantizando que el estado interno se mantenga siempre consistente y protegiendo las invariantes.

## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

### Respuesta

Una clase se considera **inmutable** cuando el estado de sus objetos no puede ser modificado en ningún momento tras su creación. Es decir, una vez que se reserva memoria y el constructor inicializa los atributos, estos permanecen como constantes de solo lectura durante toda la vida útil de la instancia (similar a trabajar con un `const struct` en C). Por el contrario, un **método modificador** (o *mutator*) es cualquier función perteneciente a la clase que altera el valor de uno o varios atributos internos, cambiando el estado del objeto a lo largo del tiempo.

Un método modificador no es siempre un "setter" tradicional. Aunque un "setter" básico (como `setCoordenadaX(double valor)`) es el ejemplo más evidente de modificador, existen muchas otras funciones que alteran el estado interno como consecuencia de una operación lógica. Por ejemplo, un método `desplazar(double incrementoX, double incrementoY)` en una clase `Punto` modifica internamente ambas coordenadas al sumarles el incremento, por lo que es un método modificador, pero no es un simple "setter" de asignación directa.

Diseñar clases inmutables presenta enormes ventajas en la arquitectura del software. Al garantizar matemáticamente que el estado nunca cambiará, se elimina por completo el riesgo de efectos secundarios imprevistos. Cuando un objeto inmutable se pasa como parámetro a una función (paso por referencia), el programador tiene la absoluta certeza de que dicha función no podrá corromper los datos originales, algo que en C requeriría un control muy estricto con punteros constantes.

Además, la inmutabilidad es una propiedad excepcionalmente valiosa para la programación concurrente (cuando el programa ejecuta varios hilos o procesos simultáneamente). Si un objeto es inmutable, múltiples partes del programa pueden leer su información al mismo tiempo sin necesidad de emplear complejos mecanismos de bloqueo de memoria para evitar "condiciones de carrera" (que un hilo lea mientras otro escribe), haciendo que el sistema sea más seguro y rápido.

## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

### Respuesta

No es en absoluto recomendable incluir métodos "setter" por defecto o como una regla automática para todos los atributos de una clase. Proceder de esta manera es considerado un antipatrón de diseño que destruye el propósito fundamental de la encapsulación y la ocultación de información. Si cada variable privada tiene un "setter" y un "getter" sin restricciones, la clase pierde el control sobre sus invariantes lógicas y vuelve a comportarse exactamente igual que un `struct` público de C.

La convención y buena práctica en el diseño orientado a objetos dicta que los atributos deben nacer siempre privados y sin métodos de modificación. Únicamente se debe programar un "setter" cuando la lógica del sistema exige de forma ineludible que un componente externo altere ese valor específico. En lugar de exponer el estado, se recomienda dotar al objeto de métodos de comportamiento (por ejemplo, `procesarPago()` en lugar de `setSaldo()`), permitiendo que sea el propio objeto quien gestione sus cambios internos.

Evitar la generación sistemática de "setters" es el paso fundamental para fomentar la inmutabilidad y proteger la consistencia de los datos. Obliga al programador a reflexionar sobre si un dato realmente necesita cambiar a lo largo de la ejecución o si es suficiente con proporcionarlo una única vez a través del constructor. Esta restricción deliberada de la interfaz pública da como resultado un código mucho más predecible, fácil de depurar y mantenible a largo plazo.

## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

### Respuesta

En Java, la clase `String` es estrictamente inmutable. Una vez que se crea un objeto de cadena en la memoria dinámica, su contenido (la secuencia de caracteres) no puede ser alterado bajo ninguna circunstancia. Esto contrasta con los arreglos de caracteres (`char[]`) en C, donde se puede acceder a un índice específico y modificar un carácter directamente en el bloque de memoria original.

Cuando se concatenan dos cadenas (utilizando el operador `+`), el sistema no amplía la memoria de la primera cadena para añadir la segunda. En su lugar, se reserva un nuevo bloque de memoria para instanciar un tercer objeto `String` que contendrá la combinación de ambos, dejando los objetos originales intactos en la memoria. Si las referencias a los objetos originales se pierden, el recolector de basura acabará liberando dicha memoria.

Por lo tanto, si se necesita construir una cadena muy larga mediante múltiples concatenaciones paso a paso (por ejemplo, dentro de un bucle), utilizar la clase `String` resulta extremadamente ineficiente por la constante reserva y liberación de memoria. En estos casos, se debe emplear la clase `StringBuilder` (o `StringBuffer` en entornos concurrentes). Esta clase actúa como un búfer mutable (similar a gestionar un arreglo dinámico con `realloc` en C) que permite añadir caracteres sin crear objetos intermedios, solicitando la conversión final a `String` únicamente cuando el proceso haya terminado.

## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java?

### Respuesta

En la programación orientada a objetos, se pueden comparar dos instancias evaluando su **identidad** o su **contenido**. La comparación por identidad (usando el operador `==`) verifica si dos referencias apuntan exactamente a la misma dirección de memoria en el Heap, lo que equivale a comparar dos punteros en C (`ptr1 == ptr2`). Por otro lado, la comparación por contenido verifica si los atributos internos de dos objetos distintos tienen los mismos valores, aunque residan en zonas de memoria completamente separadas.

En Java, el método `equals(Object o)` es el mecanismo estándar para comparar el contenido de los objetos. Sin embargo, su comportamiento por defecto (al ser heredado de la clase base universal `Object`) se limita a realizar una simple comparación de identidad mediante `==`. Para que `equals` compare el estado interno real, cada clase debe sobrescribir este método especificando lógicamente qué significa que dos de sus instancias sean "iguales" (por ejemplo, programando que se compruebe que las coordenadas `x` e `y` coincidan).

Debido a esto, para comparar dos cadenas en Java **nunca** se debe utilizar el operador aritmético `==`. Dado que las cadenas son objetos, `==` solo confirmaría si son el mismo objeto exacto en memoria, no si contienen el mismo texto. La forma correcta e indispensable de comparar el valor de dos cadenas es invocando el método `cadena1.equals(cadena2)`, el cual ya está internamente programado en la clase `String` para recorrer y comparar los arreglos de caracteres subyacentes uno a uno, al estilo de la función `strcmp` en C.

## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers?

### Respuesta

Las clases "wrapper" (envoltorio) son clases estándar diseñadas para encapsular un tipo de dato primitivo dentro de un objeto real. En lenguajes como Java, los tipos primitivos (`int`, `char`, `double`) no son objetos por cuestiones de rendimiento; se comportan como variables atómicas almacenadas directamente en la pila (Stack), de forma idéntica a como operan en C. Las clases wrapper (como `Integer`, `Character` o `Double`) proporcionan una "caja" en el Heap para alojar ese valor numérico, dotándolo de métodos y comportamientos propios de un objeto.

El proceso de convertir un primitivo en su envoltorio o viceversa suele ser automático en las versiones modernas de Java, gracias a una característica del compilador denominada *autoboxing* (empaquetado automático) y *unboxing* (desempaquetado). El compilador detecta en tiempo de compilación cuándo se espera un objeto y se le pasa un primitivo, e inserta el código subyacente de forma invisible para realizar la conversión por el programador.

La principal ventaja de estas clases es que permiten utilizar tipos numéricos básicos en contextos que exigen estrictamente objetos. Por ejemplo, las estructuras de datos dinámicas en la biblioteca estándar de Java (como listas o diccionarios, que reemplazan la gestión manual de nodos con punteros en C) no admiten primitivos. Además, al ser objetos, estas variables pueden tomar el valor `null` (indicando ausencia de dato) y ofrecen funciones de utilidad integradas, como la conversión desde cadenas de texto (`Integer.parseInt`).

No todos los lenguajes orientados a objetos requieren o poseen esta dualidad. Lenguajes puramente orientados a objetos como Python, Ruby o Smalltalk dictan que "todo es un objeto", incluyendo los números enteros más elementales. En ellos no existen tipos primitivos separados que necesiten ser envueltos, lo que simplifica la lógica del sistema a costa de un mayor consumo general de recursos.

## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

### Respuesta

En la programación, un **tipo de dato enumerado** es un tipo especial que restringe las variables a alojar únicamente uno de los pocos valores predefinidos en una lista. En C, se utiliza la palabra clave `enum` para definir conjuntos de constantes enteras asociadas a nombres legibles. Sin embargo, bajo el capó no dejan de ser simples números, careciendo de comprobación de tipos estricta y permitiendo que se operen aritméticamente de forma insegura.

En Java, un tipo de dato enumerado (definido con la palabra reservada `enum`) es, en realidad, una clase especial y completamente funcional. El compilador de Java genera automáticamente una clase cuyo constructor es inaccesible desde el exterior y que expone instancias estáticas, públicas e inmutables para cada uno de los valores definidos en la enumeración.

La ventaja fundamental en términos de encapsulación es la enorme seguridad de tipos (Type Safety). Al ser objetos reales y no simples identificadores numéricos, resulta imposible asignar un valor arbitrario o inválido a una variable de tipo enumerado. Además, al tratarse de clases completas, los enumerados en Java pueden contener sus propios atributos privados, constructores internos y métodos específicos, encapsulando la lógica de negocio directamente vinculada a cada constante dentro de la propia enumeración.

## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado.

### Respuesta

Para implementar este enumerado en Java, se definen primero las doce instancias al inicio del bloque, separadas por comas. Estas instancias invocan implícitamente al constructor privado de la enumeración al momento de cargar la clase. A diferencia de un `enum` clásico en C, aquí se pueden pasar argumentos a cada valor durante su declaración, asociando un estado fijo a cada mes.

A continuación, se define la estructura interna: los atributos privados (días y ordinal), el constructor (que es privado por defecto en las enumeraciones para evitar la creación externa de nuevos meses) y los métodos consultores que conformarán la interfaz pública para acceder a esa información inmutable.

```java
public enum Mes {
    // Instancias de la enumeración con sus argumentos para el constructor
    ENERO(31, 1), FEBRERO(28, 2), MARZO(31, 3), 
    ABRIL(30, 4), MAYO(31, 5), JUNIO(30, 6), 
    JULIO(31, 7), AGOSTO(31, 8), SEPTIEMBRE(30, 9), 
    OCTUBRE(31, 10), NOVIEMBRE(30, 11), DICIEMBRE(31, 12);

    // Atributos privados (encapsulados e inmutables)
    private final int dias;
    private final int ordinal;

    // Constructor (implícitamente privado en los enums)
    Mes(int dias, int ordinal) {
        this.dias = dias;
        this.ordinal = ordinal;
    }

    // Interfaz pública: Métodos getters
    public int getDias() {
        return this.dias;
    }

    public int getOrdinal() {
        return this.ordinal;
    }
}

```

## 24. Añade a la clase `Mes` del ejercicio anterior cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`

### Respuesta

Para añadir esta funcionalidad sin romper la encapsulación, se incluyen los métodos solicitados directamente dentro de la definición del bloque `enum`. Al formar parte de la clase, cada instancia (por ejemplo, `Mes.ENERO`) conoce su propio estado privado (su `ordinal`) y puede emplearlo en las operaciones lógicas, liberando al código principal de tener que realizar estructuras `switch` repetitivas.

En la implementación, se evalúa el atributo `ordinal` junto al parámetro booleano recibido para determinar la estación. Se ha utilizado una lógica matemática basada en el número de mes, asumiendo meses enteros para simplificar el modelo (por ejemplo, considerando invierno en el hemisferio norte a los meses 12, 1 y 2).

```java
    // ... (Se añade a continuación del código anterior del enumerado Mes) ...

    public boolean esDeInvierno(boolean enHemisferioNorte) {
        if (enHemisferioNorte) {
            return this.ordinal == 12 || this.ordinal == 1 || this.ordinal == 2;
        } else {
            return this.ordinal == 6 || this.ordinal == 7 || this.ordinal == 8;
        }
    }

    public boolean esDePrimavera(boolean enHemisferioNorte) {
        if (enHemisferioNorte) {
            return this.ordinal >= 3 && this.ordinal <= 5;
        } else {
            return this.ordinal >= 9 && this.ordinal <= 11;
        }
    }

    public boolean esDeVerano(boolean enHemisferioNorte) {
        if (enHemisferioNorte) {
            return this.ordinal >= 6 && this.ordinal <= 8;
        } else {
            return this.ordinal == 12 || this.ordinal == 1 || this.ordinal == 2;
        }
    }

    public boolean esDeOtoño(boolean enHemisferioNorte) {
        if (enHemisferioNorte) {
            return this.ordinal >= 9 && this.ordinal <= 11;
        } else {
            return this.ordinal >= 3 && this.ordinal <= 5;
        }
    }

```
---