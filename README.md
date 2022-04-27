# Actividad 3.2 Programando un DFA

![ITESM](https://user-images.githubusercontent.com/72751268/165425399-ae291351-e636-4149-8747-16ef1d941902.jpg)


## Instituto Tecnológico y de Estudios Superiores de Monterrey Campus Monterrey

### Implementación de métodos computacionales (TC2037.601)

#### Realizada por:

- Sergio Alejandro Esparza González - A01625430
- Eric Eugenio Oyervides Espino - A01570760
- Alejandro Mauricio Maqueo Huerta - A01620649

#### Semestre:

Febrero-junio 2022

#### Fecha:

03 de abril de 2022

## Nota importante
Todos los archivos de esta entrega deberán formar parte de una misma carpeta (no comprimida) para su correcta visualización.

## Propósito
Una de las aplicaciones de los autómatas finitos determinísticos es la implementación de reconocedores de tokens en un lenguaje de programación (conocido como Lexer en 
los compiladores).

En esta actividad deberás hacer un programa que reciba como entrada un archivo con una serie de expresiones aritméticas, escritas bajo ciertas reglas, y entregará como 
salida el conjunto de tokens reconocidos, indicando su tipo, o indicando que hay un error en su formación, es decir, no se respetaron las reglas establecidas.

## Instrucciones
Hacer un programa que reciba como entrada un archivo de texto que contenga expresiones aritméticas y comentarios, y nos regrese una tabla o lista con cada uno de sus 
tokens encontrados, en el orden en que fueron encontrados e indicando de qué tipo son. (Pueden usar el lenguaje de programación de su preferencia).

### Tipos de tokens

Las expresiones aritméticas sólo podrán contener los siguientes tipos de tokens:

- Enteros
- Flotantes (Reales)
- Operadores:
  - Asignación
  - Suma
  - Resta
  - Multiplicación
  - División
  - Potencia
- Identificadores:
  - Variables
- Símbolos especiales:
  - (
  - )
- Comentarios:
  - // seguido de caracteres hasta que se acabe el renglón

### Entrada

- Un archivo tipo texto que contenga una o más expresiones aritméticas, una por renglón.
- Los tokens no necesariamente deben estar separados por un blanco, o pueden tener separación de más de un blanco.

Por ejemplo:

b = 7

a = 32.4 * ( -8.6 - b ) / 6.1E-8

d = a ^ b // Esto es un comentario

### Salida

Debe entregar la siguiente salida:

| Token                    | Tipo                  |
| ------------------------ | --------------------- |
| b                        | Variable              |
| =                        | Asignación            |
| 7                        | Entero                |
| a                        | Variable              |
| =                        | Asignación            |
| 32.4                     | Real                  |
| *                        | Multiplicación        |
| (                        | Paréntesis que abre   |
| -8.6                     | Real                  |
| -                        | Resta                 |
| b                        | Variable              |
| )                        | Paréntesis que cierra |
| /                        | División              |
| 6.1E-8                   | Real                  |
| d                        | Variable              |
| =                        | Asignación            |
| a                        | Variable              |
| ^                        | Potencia              |
| b                        | Variable              |
| // Esto es un comentario | Comentario            |

### Reglas de formación de algunos tokens

1. Variables:
    - Deben empezar con una letra (mayúscula o minúscula).
    - Sólo están formadas por letras, números y underscore ('_').
2. Números reales (de punto flotante):
    - Pueden ser positivos o negativos
    - Pueden o no tener parte decimal pero deben contener un punto (e.g. 10. o 10.0).
    - Pueden usar notación exponencial con la letra E, mayúscula o minúscula, pero después de la letra E sólo puede ir un entero positivo o negativo (e.g. 2.3E3, 
      6.345e-5, -0.001E-3, .467E9).
3. Comentarios:
    - Inician con // y todo lo que sigue hasta que termina el renglón es un comentario.

### Algoritmo

- El reconocimiento de tokens se debe hacer por medio de la **tabla de transición** de un Autómata Finito Determinístico. (La tabla puede venir en el README.md o en un 
  archivo adicional).
- El diseño del autómata debe ser parte fundamental de la documentación (utilice alguna herramienta computacional para dibujarlo, no lo haga a mano). Deben entregar el 
  .json para correrlo en automatonsimulator.
  
### Documentación:

1. Manual del usuario, indicando cómo correr su programa y qué se obtiene de salida, en qué lenguaje lo hizo y qué tengo que instalar para que funcione (puede ser un README).
2. El autómata que resuelve el problema (como un anexo del punto 1).

### Nota:

Siempre habrá espacios entre los tokens.

## Resolución

### Autómata

![Automata](https://user-images.githubusercontent.com/72751268/165425373-f9a057d8-187d-49f4-82da-2839b11adafa.png)


Quíntupla:

M = {Q, Σ, δ, q, F}

Donde:

Q = {start, Variable, Suma, Potencia, Resta, Multiplicación, Asignación, Paréntesis que abre, División, Comentario, Paréntesis que cierra, Entero, iExp, dot, Real, 
iExpS, fExpS, fExp}

Σ = {L, +, ^, #, -, *, =, (, ), /, _, ., e, E, C}

δ = {((start, L), Variable), ((start, +), Suma), ((start, ^), Potencia), ((start, #), Entero), ((start, -), Resta), ((start, *), Multiplicación), ((start, =), 
Asignación), ((start, (), Paréntesis que abre), ((start, )), Paréntesis que cierra), ((start, /), División), ((Variable, L), Variable), ((Variable, #), Variable), 
((Variable, _), Variable), ((Suma, #), Entero), ((Resta, #), Entero), ((División, /), Comentario), ((Comentario, L), Comentario), ((Comentario, #), Comentario), 
((Comentario, +), Comentario), ((Comentario, -), Comentario), ((Comentario, *), Comentario), ((Comentario, /), Comentario), ((Comentario, ^), Comentario), 
((Comentario, =), Comentario), ((Comentario, C), Comentario), ((Entero, #), Entero), ((Entero, e), iExp), ((Entero, E), iExp), ((Entero, .), dot), ((dot, #), Real), 
((iExp, +), iExpS), ((iExp, -), iExpS), ((iExp, #), Real), ((Real, #), Real), ((Real, E), fExp), ((Real, e), fExp), ((iExpS, #), Real), ((fExpS, #), Real), ((fExp, +), 
fExpS), ((fExp, -), fExpS), ((fExp, #), Real)}

q = start

F = {Variable, Suma, Potencia, Resta, Multiplicación, Asignación, Paréntesis que abre, División, Comentario, Paréntesis que cierra, Entero, Real}

El autómata se realizó en Automaton Simulator, no obstante, se tomaron algunas consideraciones (no tomadas en cuenta para el código) que limitan su funcionamiento, 
pues este software sólo permite un máximo de 10 transiciones de salida por cada estado:

1. En el alfabeto del autómata, 'L' representa cualquier letra del alfabeto inglés.
2. De igual manera que el punto anterior, '#' Representa cualquier dígito del sistema de numeración decimal.
3. 'C' representa cualquier otro carácter existente del estándar Unicode, no mencionado explícitamente en el alfabeto del autómata.
4. Los símbolos 'e' y 'E' del alfabeto, son parte de 'L' por ser letras, sin embargo, para el estado "Entero" se colocaron específicamente estas transiciones, pues son 
   las únicas letras que acepta para avanzar al estado "iExp".
5. Caso similar a la consideración 4, para pasar del estado "Real" al estado "fExp".
6. En el estado de "Comentario", 'C' representa todos los caracteres que no tienen transición a partir de este mismo estado (_, ., (, ), y cualquier otro carácter 
   Unicode como se mencionó en el punto 3).
7. Los espacios no son tomados en cuenta, por lo que es mejor que el autómata pruebe token por token en lugar de línea por línea, de un código.
8. Si un espacio es forzosamente requerido, por ejemplo, en el estado "Comentario", se puede reemplazar por C para poder ser tomado en cuenta.
9. Similar al punto anterior, tampoco se toman en cuenta tabulaciones, saltos de línea, caracteres vacíos o similares.

Así, por ejemplo, el siguiente código:

b = 7

a = 32.4 * ( -8.6 - b ) / 6.1E-8

d = a ^ b // Esto es un comentario

Se tendría que probar de la siguiente forma:

L = #

L = ##.# * ( -#.# - L ) / #.#E-#

L = L ^ L //CLLLLCLLCLLCLLLLLLLLLL

Y sin olvidar probar token por token (cada palabra separada por espacios, la primera y la última de cada línea).

### Tabla de transiciones

![Matriz](https://user-images.githubusercontent.com/72751268/165425351-0013de66-d009-4884-99f4-d4ab4ba274ae.jpg)


En la tabla o matriz de transiciones se muestra lo que hace cada estado en el programa según el carácter que se reciba.

Nótense los siguientes cambios:

1. ‘#’ se representa aquí como el conjunto de los dígitos del 0 al 9 ({0-9}).
2. Se separan en dos columnas distintas la letra ‘e’ (E || e) y el resto de las letras del alfabeto inglés ({A-D} ∪ {F-Z} ∪ {a-d} ∪ {f-z}).
3. El carácter espacio (\s) aquí sí es tomado en cuenta.
4. Todos los demás caracteres no puestos de forma explícita en los encabezados de la tabla de transiciones se representan como “Otro”.
5. Se tienen las siguientes equivalencias numéricas de estados: 0 = start, 1 = iExp, 2 = iExpS, 3 = dot, 4 = fExp, 5 = fExpS, 6 = Suma, 7 = Resta, 8 = Asignación, 
   9 = Multiplicación, 10 = Potencia, 11 = Paréntesis que abre, 12 = Paréntesis que cierra, 13 = Entero, 14 = División, 15 = Variable, 16 = Real, 17 = Comentario,
   18 = Error.
7. Aquí se añadió el estado “Error”, que es aquel al que va un estado cuando recibe un carácter que no acepta.

### Código

El lenguaje utilizado es C++; el código se encuentra escrito en el archivo "main.cpp". Para correr el programa se necesita un compilador que soporte C++11 o versiones 
posteriores, tal como GCC 4.3 o una versión posterior, y un IDE que use este compilador, como DEV C++.

#### Alcance global

```C++
{

//**********************************************************************************************************
// Actividad 3.2 Programando un DFA
// Programa que recibe como entrada un archivo con una serie de expresiones aritmeticas, escritas bajo
// ciertas reglas, y entrega como salida el conjunto de tokens reconocidos, indicando su tipo, o indicando
// que hay un error en su formacion, es decir, no se respetaron las reglas establecidas.
// Creado por: Sergio Alejandro Esparza Gonzalez - A01625430, Alejandro Mauricio Maqueo Huerta - A01620649,
// Eric Eugenio Oyervides Espino - A01570760.
// Ultima modificacion realizada el: 03 de abril de 2022.
//**********************************************************************************************************

//Librerias:
#include <iostream> //Flujo de datos.
#include <conio.h>  //Consola.
#include <vector>   //Vectores.
#include <fstream>  //Archivos.
#include <string>   //Cadenas.

//Espacios de nombres:
using namespace std; //Estandar.

//Constantes:
const int ERROR = 18; //Estado de error.

//Variables globales:
ifstream inFile; //Leer datos de un archivo.
int transitionMatrix[19][15] = 
  { 
  {6,     7,     8,     9,     10,    13,    11,    12,    14,    15,    15,    ERROR, ERROR, 0,     ERROR},
  {2,     2,     ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {5,     5,     ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 13,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 13,    ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 13,    ERROR, ERROR, ERROR, ERROR, 1,     ERROR, 3,     0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 17,    ERROR, ERROR, ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 15,    ERROR, ERROR, ERROR, 15,    15,    15,    ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, 4,     ERROR, ERROR, 0,     ERROR},
  {17,    17,    17,    17,    17,    17,    17,    17,    17,    17,    17,    17,    17,    17,    17   },
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 0,     ERROR}
  }; //Matriz de transiciones.

//Prototipos de funciones:
int filter(char); //Obtener numero de columna de la matriz de transiciones.
string getType(int); //Obtener el tipo de un token.
void printVector2D(vector< vector<string> >); //Imprimir elementos de un vector de cadenas de 2 dimensiones.

}
```

Todo lo declarado aquí será alcanzable en cualquier parte del código. Primero se colocaron los datos generales de la actividad como comentarios: nombre de la 
actividad, descripción general, nombres de los integrantes del equipo y fecha de la última modificación. Después se pusieron las librerías necesarias para el 
programa, para trabajar con el flujo de datos, la consola, vectores, archivos, y cadenas. Posteriormente, se añadieron los espacios de nombres a usar, siendo el 
estándar en este caso. Luego, se definió la constante entera "ERROR", con valor de 18, que indica el estado de error; abajo de esta, aparecen las variables globales, 
como "inFile" (de tipo istream) para poder leer datos de un archivo, y "transitionMatrix", siendo una matriz de tipo entero que representa la matriz de transiciones 
definida previamente. Finalmente, se declararon los prototipos de las funciones a usar en el código, como "filter()", "getType()" y "printVector2D()", que serán 
analizadas más adelante.

#### Función principal

```C++
{

//Funcion principal. Parametros: no. Retorno: 0 (int):
int main() {

  //Variables locales:
  vector<string> lineas; //Vector que guarda todas las lineas del archivo.
  string linea = ""; //Almacena cada linea del archivo.
  string value = ""; //Almacena todos los caracteres de un token.
  int index = 0; //Indice que va recorriendo cada linea del vector.
  char c = ' '; //Almacena cada caracter del vector de lineas.
  int nLinea = 0; //Iterador que recorre cada elemento del vector de lineas.
  int state = 0; //Indica el estado en el que se encuentra el DFA.
  int past = 0; //Guarda el estado pasado.
  vector<string> grupo; //Guarda un token con su tipo respectivo.
  vector< vector<string> > grupos; //Guarda todos los tokens con su tipo respectivo.
  
  //Codigo:
  
  inFile.open("prueba.txt"); //Abrir archivo.
  
  //Almacenar lineas del archivo:
  while (!inFile.eof()) { //Mientras el archivo no se acabe:

    getline(inFile, linea); //Se obtiene una de sus lineas.
    lineas.push_back(linea); //Se guarda en el vector lineas.
    
  }

  inFile.close(); //Cerrar archivo.
  
  //Leer elementos del vector:
  while (nLinea < lineas.size()) { //Mientras haya lineas en el vector.
    
    do { //Hacer...

      past = state; //Se guarda el estado actual para que se mantenga como el anterior en esta iteracion.
      c = lineas.at(nLinea)[index]; //Se obtiene el caracter de una linea.
      index++; //Para pasar al siguiente caracter en la siguiente vuelta.
      state = transitionMatrix[state][filter(c)]; //Se obtiene el estado actual de la matriz de transiciones segun c.
      
      if (state != 0) //Si es un estado diferente al inicial...
        value += c; //Se aniade el caracter actual al token.
      
      if (state == 0 && index > 0 && past != 0) { //Si se ha llegado a un espacio, sin que sea el inicio de la cadena 
      //o que el caracter anterior haya sido un espacio...

        grupo.push_back(value); //Se almacena el token obtenido en un vector unidimensional.
        grupo.push_back(getType(past)); //Se almacena el tipo del token, segun el estado pasado (antes de ser 0) en un
        //vector unidimensioal.
        grupos.push_back(grupo); //Se juntan el token y su tipo en un vector bidimensional.
        grupo.clear(); //Se limpia el vector unidimensional para almacenar otro token con su respectivo tipo.
        value = ""; //Se elimina el token obtenido para guardar otro token.
        
      }
      
      if (index == lineas.at(nLinea).length() && state != 0) { //Si se llego al final de la linea sin que el ultimo 
      //estado sea un espacio...
        
        grupo.push_back(value); //Se almacena el token obtenido en el vector 1D.
        grupo.push_back(getType(state)); //Se almacena en el vector 1D el tipo del token, segun el estado actual.
        
        grupos.push_back(grupo); //Se juntan el token y su tipo en un vector 2D.
        grupo.clear(); //Se limpia el vector 1D para almacenar otro token con su respectivo tipo.
        value = ""; //Se reinicia value para guardar otro token.
        
      }
  
    } while (index < lineas.at(nLinea).length()); //Mientras no se llegue al final de la linea. 

    nLinea++; //Vamos hacia la siguiente linea.
    index = 0; //El iterador debe de comenzar al inicio de la linea.
    value = ""; //Se reinicia el valor de value para guardar otro token.
    state = 0; //El estado del inicio de la linea es 0.
    past = 0; // El estado pasado sera 0 de igual forma.
    
  }

  //Formato de la salida:
  cout.width(80); //Se dejan 80 espacios para cada escritura, parecido a una tabla, pues 80 caracteres es una
  //convencion informal del maximo que debe de tener una linea de codigo.
  cout << left; //Se alinea la escritura a la izquierda de los espacios.
  cout << "Token"; //Los tokens estaran debajo de esta palabra.
  cout << "Tipo"; //Los tipos de tokens estaran debajo de esta palabra.
  cout << endl; //Se comenzaran a imprimir los elementos en la siguiente linea.

  printVector2D(grupos); //Imprimir la matriz de tokens y tipos respectivos.
  
  getch(); //Hacer que se necesite pulsar una tecla mas para cerrar la consola.
  
  return 0; //Fin de main.

}

}
```
La función "main()", es la principal del programa, parte del mismo lenguaje. En ella se declararon primero las variables locales como "lineas" (vector de strings que 
guarda todas las líneas del archivo que será leído después, "linea" (cadena que almacena cada línea del archivo mencionado), "value" (string que almacena todos los 
caracteres de un token), "index" (índice entero que va recorriendo cada línea del vector), "c" (char que almacena cada carácter del vector de líneas), "nLinea" 
(iterador entero que recorre cada elemento del vector de líneas), "state" (entero que indica el estado en el que se encuentra el DFA), "past" (entero que guarda el 
estado pasado del DFA), "grupo" (vector de strings que guarda un token con su tipo respectivo), y "grupos" (matriz de cadenas que guarda todos los tokens con su tipo 
respectivo).

Posteriormente, se abre el archivo "prueba.txt" para leer sus datos (para cambiar el archivo a leer, sólo se cabia el nombre y formato del archivo en la línea 71 del 
archivo "main.cpp" \[correspondiente a la 281 de este archivo MD]); luego, mientras no se llegue al final del archivo, se almacena cada una de las líneas del archivo en la string "linea", y cada línea se va guardando en el vector "lineas" para poder tenerlas todas, después se cierra el archivo. 

Luego, se leen los elementos del vector de líneas, mientras haya elementos en ese vector, se repite un ciclo: el estado actual se hace el pasado para la iteración 
actual, se obtiene el caracter de una de las líneas del vector, se aumenta "index" en 1 para pasar al siguiente caracter en la siguiente iteración, se obtiene el 
estado actual según ese caracter leído (se usa la función "filter()" que se analizará después); si el estado actual es diferente a 0, se añade el caracter leído al 
token; si el estado es 0, el índice es mayor a 0, y el estado anterior es diferente a 0, significa que se ha llegado a un espacio sin que sea el inicio de la línea o 
que el caracter anterior sea un espacio, por lo que se almacena el token obtenido en el vector "grupo", junto con su tipo (utilizando la función "getType()" que se 
analizará más delante) y este grupo se almacena junto con los demás en el vector "grupos", se limpia lo almacenado en el vector "grupo" para almacenar otro grupo 
cuando se necesite, y "value" pasa a ser vacío para guardar otro token cuando sea necesario.

Después, si se ha llegado al final de la línea y el estado no es un espacio, se hace lo mismo del párrafo anterior, guardando el token y su tipo en el vector "grupo", 
y guardando ese grupo en el vector "grupos", limpiando el vector "grupo" y la cadena "value". Este ciclo anidado se repetirá mientras no se llegue al final de la línea 
guardada, pero el ciclo principal continúa mientras sigan habiendo líneas, y acabando el ciclo anidado, "nLinea" aumenta en 1 para recorrer la siguiente línea con el 
ciclo anidado otra vez en la siguiente vuelta del ciclo principal, "index" se hace 0 de nuevo, "value" se limpia, "state" se hace 0, y "past" se hace 0 también.

Finalmente, se declara el formato de la salida, dejando 80 espacios para cada escritura, haciendo algo parecido a una tabla, pues 80 espacios es una convención 
informal del máximo de caracteres que debe de tener una línea de código. Después se alinea la escritura a la izquierda, y se escribe como encabezados "Token" y "Tipo", 
para luego mostrar todos los tokens con su tipo respectivo. Se añade un salto de línea para separar los encabezados de los demás elementos, y se llama a la función 
"printVector2D()" (su funcionamiento se analiza al final de este archivo) para mostrar los elementos del vector "grupos". "La función "getch()" es para que se necesite 
pulsar una tecla más para cerrar la consola y asegurar que no se cierre de repente, y al final, "main()" retorna 0 si todo sale bien.

#### Función "filter()"

```C++
{

/*
Funcion para obtener el numero de columna de la matriz de transiciones segun un determinado caracter.
Parametros: caracter (c) a probar (char).
Retorno: numero de columna de la matriz(int):
*/
int filter(char c) {

  //Verificar coincidencia de c:
  if (c == '+') //Si es un '+' (es el digito que el usuario ve)...
    return 0;
  else if (c == '-')
    return 1;
  else if (c == '=')
    return 2;
  else if (c == '*')
    return 3;
  else if (c == '^')
    return 4;
  else if (c >= 48 && c <= 57) //Si es un digito (son sus valores ASCII)...
    return 5;
  else if (c == '(')
    return 6;
  else if (c == ')')
    return 7;
  else if (c == '/')
    return 8;
  else if ((c >= 65 && c <= 68) || (c >= 70 && c <= 90) || (c >= 97 && c <= 100) || (c >= 102 && c <= 122))
  //Si es una letra que no sea 'e' o 'E':
    return 9;
  else if (c == 'E' || c == 'e')
    return 10;
  else if (c == '_')
    return 11;
  else if (c == '.')
    return 12;
  else if (c == ' ')
    return 13;
  else //Cualquier otro caracter:
    return 14;
   
}

}
```

Según un determinado caracter (c), siendo el parámetro de la función, se retorna un distinto valor entero, que representa el número de columna de la matriz de 
transiciones, pudiéndose observar en la estructura condicional, los símbolos del alfabeto del autómata, como '+', '-', '=', las letras del alfabeto inglés, el caracter 
de espaciado, etc.

#### Función "getType()"

```C++
{

/*
Funcion que obtiene el tipo de token segun un estado final del DFA.
Parametros: estado final (s) del DFA (int).
Retorno: tipo del token (string).
*/
string getType(int s) {

  switch(s) { //Segun sea el valor de s, se retorna el tipo del token correspondiente.

    case 6:
      return "Suma";
    case 7:
      return "Resta";
    case 8:
      return "Asignacion";
    case 9:
      return "Multiplicacion";
    case 10:
      return "Potencia";
    case 11:
      return "Parentesis que abre";
    case 12:
      return "Parentesis que cierra";
    case 13:
      return "Entero";
    case 14:
      return "Division";
    case 15:
      return "Variable";
    case 16:
      return "Real";
    case 17:
      return "Comentario";
    default: //Cualquier otro estado que se haga pasar por final...
      return "Error";
        
  }

}

}
```

Se obtiene el tipo de token según el estado final del DFA (s) que es pasado como parámetro entero. Retorna una string que es el tipo de token; por ello, se puede 
observar en el "switch" que hay strings como "Suma", "Resta", "Potencia", "Parentesis que abre", etc., pues son los distintos estados finales según lo visto en el 
autómata.

#### Función "printVector2D()"

```C++
{

/*
Funcion que imprime los elementos de una matriz de strings.
Parametros: matriz (v) de strings (vector< vector<string> >).
Retorno: nada.
*/
void printVector2D(vector< vector<string> > v) {

  cout << endl; //Salto de linea para diferenciar los encabezados del resto de elementos de la tabla.
  
  for (int i = 0; i < v.size(); i++) { //Recorre cada vector de la matriz...

    cout.width(79); //Se dejan 79 espacios para la salida (80 no por el espacio adicional entre elementos, ver for 
    //siguiente).
    cout << left; //La salida se alinea a la izquierda.
    
    for (int j = 0; j < v.at(i).size(); j++) { //Recorre cada elemento de cada vector...

      cout << v.at(i).at(j) << " "; //Se imprime asi un elemento del vector mas un espacio por si llegara a ser de
      //mas de 79 caracteres, que por lo menos lo separe un espacio del siguiente elemento.
      
    }

    cout << endl; //Salto de linea para imprimir los elementos del siguiente vector de la matriz.
    
  }
  
}

}
```

Se imprimen los elementos de una matriz de strings pasada como parámetro, no retorna algún valor, pues todo se hace dentro de la función. Mediante un ciclo anidado con 
otro, se va recorriendo por ejemplo, la matriz de grupos: el primer ciclo recorre los grupos y prepara la salida tipo tabla (dejando 79 espacios para cada escritura y 
alineando el texto a la izquierda), y el ciclo anidado recorre los elementos de cada grupo, imprimiendo un token con su tipo respectivo (separados por un espacio 
extra, pues así si llega a ser el token de más de 78 caracteres, por lo menos se separa con un espacio de su tipo), y en el ciclo principal, se salta una línea para 
imprimir el grupo siguiente en el renglón siguiente.

#### Prueba

Utilizando el ejemplo del inicio (en el archivo "prueba.txt"):

![TXT](https://user-images.githubusercontent.com/72751268/165425260-4738eee0-a6d1-4da2-864d-6cfec40de07a.png)

Al ejecutar el programa, se muestra lo siguiente:

![EXE](https://user-images.githubusercontent.com/72751268/165425292-5874be9f-c3c9-4aba-b5a0-389dea15ddc0.jpg)

Obteniendo así los resultados deseados.

## Nota para el docente

Para más casos de prueba, se recomienda modificar directamente el archivo "prueba.txt" para no tener que modificar el código.




