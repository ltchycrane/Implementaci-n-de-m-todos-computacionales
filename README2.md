# Actividad 3.2 Programando un DFA

![Tec](https://user-images.githubusercontent.com/72751268/165428589-cbd94e91-7b63-4ca7-a461-b69038917e4e.jpg)

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

## Descripción de la Evidencia:

En equipos de 3 personas:

1. El Syntax Highlighter utilizará el output de su programa anterior: necesita una lista de tokens la cual va a procesar para poder determinar si el orden es correcto o no.
2. Usando el lenguaje de su preferencia, implementen un analizador de sintaxis por método de descenso recursivo
  1. Pueden tener el ; como token final de cada expresión. Considerar cuando hay comentarios
3. El programa debe convertir su entrada en documentos de HTML+CSS que resalten su léxico
4. Incluye un README, donde venga la gramática de tu analizador. Y todo lo necesario para ejecutar tu código.
5. Utiliza unit testing e incluyelo en tu proyecto
6. Utiliza las convenciones de codificación del lenguaje en el que está implementado tu programa (ej.; si estás usando Python, asegurate de seguir el PEP8).
7. Realiza un video donde se muestre el correcto funcionamiento de su programa

Adicionalmente, habrá que hacer una reflexión de manera individual (un documento por miembro de equipo):

1. Reflexiona sobre la solución planteada, los algoritmos implementados y sobre el tiempo de ejecución de los mismos.
2. Calcula la complejidad de tu algoritmo
3. Plasma en un breve reporte de una página las conclusiones de tu reflexión en los puntos 1 y 2 de este inciso. Agrega además una breve reflexión sobre las implicaciones éticas que el tipo de tecnología que desarrollaste pudiera tener en la sociedad.

##Solución

### Código

El lenguaje utilizado es C++; el código se encuentra escrito en el archivo "main.cpp". Para correr el programa se necesita un compilador que soporte C++11 o versiones 
posteriores, tal como GCC 4.3 o una versión posterior, y un IDE que use este compilador, como DEV C++.

```C++
//*******************************************************************************
// Actividad Integradora 1, Resaltador de sintaxis (evidencia de competencia).
// Programa que recibe como entrada un archivo con una serie de expresiones 
// aritmeticas, escritas bajo ciertas reglas, y entrega como salida documentos 
// de HTML+CSS que resalten ese lexico.
// Creado por: Sergio Alejandro Esparza Gonzalez - A01625430, Alejandro Mauricio
// Maqueo Huerta - A01620649, Eric Eugenio Oyervides Espino - A01570760.
// Ultima modificacion realizada el: 26 de abril de 2022.
//*******************************************************************************

//Librerias:
#include <iostream> //Flujo de datos.
#include <vector>   //Vectores.
#include <fstream>  //Archivos.
#include <string>   //Cadenas.

//Espacios de nombres:
using namespace std; //Estandar.

//Constantes:
const int ERROR = 18; //Estado de error.

//Variables globales:
ifstream inFile; //Leer datos de un archivo.
ofstream outFile; //Escribir datos en un archivo.
int transitionMatrix[19][15] = 
  { 
  {6,     7,     8,     9,     10,    13,    11,    12,    14,    15,    15,    
   ERROR, ERROR, 0,     ERROR},
  {2,     2,     ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR},
  {5,     5,     ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 13,    ERROR, ERROR, ERROR, ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 13,    ERROR, ERROR, ERROR, ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 13,    ERROR, ERROR, ERROR, ERROR, 1,
   ERROR, 3,     0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, 17,    ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 15,    ERROR, ERROR, ERROR, 15,    15,
   15,    ERROR, 0,     ERROR},
  {ERROR, ERROR, ERROR, ERROR, ERROR, 16,    ERROR, ERROR, ERROR, ERROR, 4,
   ERROR, ERROR, 0,     ERROR},
  {17,    17,    17,    17,    17,    17,    17,    17,    17,    17,    17,
   17,    17,    17,    17   },
  {ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR, ERROR,
   ERROR, ERROR, 0,     ERROR}
  }; //Matriz de transiciones.
vector< vector< vector<string> > > gruposLineas; //Guarda los tokens y tipos 
  //linea por linea.
vector<string> lineasCorrectas; //Indica si la gramatica de una linea es     
  //correcta o no.
int nPrueba = 0; //Numero de prueba del codigo.

//Prototipos de funciones:
void lexer(); //Analizador lexico.
int filter(char); //Obtener numero de columna de la matriz de transiciones.
string getType(int); //Obtener el tipo de un token.
void printVector1D(vector<string>); //Imprimir elementos de un vector de
  //cadenas de 1 dimension.
void printVector2D(vector< vector<string> >); //Imprimir elementos de un
  //vector de cadenas de 2 dimensiones.
void printVector3D(vector< vector< vector<string> > >); //Imprimir elementos
  //de un vector de cadenas de 3 dimensiones.
void parser(); //Analizador sintactico.
bool parseMenu(vector<string>&); //Comienza el parseo por descenso recursivo.
bool parsePAO(vector<string>&); //Parseo de la regla PAO.
bool parsePA(vector<string>&); //Parseo de la regla PA.
bool parseAO(vector<string>&); //Parseo de la regla AO.
bool parseA(vector<string>&); //Parseo de la regla A.
bool parseM(vector<string>&); //Parseo de la regla M.
bool parseC(vector<string>&); //Parseo de la regla C.
bool parseT(vector<string>&); //Parseo de la regla T.
bool parseO(vector<string>&); //Parseo de la regla O.
bool parseP(vector<string>&); //Parseo de la regla P.
bool parseOP(vector<string>&); //Parseo de la regla OP.
void highlightHTML(); //Resaltador de sintaxis.
bool unitTesting(); //Realizar unit testing.
bool match(string[]); //Hacer match de resultados reales y esperados.
```

## Main
```C++
/*
Funcion principal.
Parametros: no. 
Retorno: 0 (int):
*/
int main() {

  //Realizar unit testing:
  if (unitTesting()) { //Si los resultados salen bien...
    cout << "\nEl programa funciona correctamente." << endl;
    highlightHTML(); //Se usa el resaltador de sintaxis.
    
  }
    
  else //Si no...
    cout << "\nEl programa no funciona como deberia." << endl; //Se indica que
      //no, y ahí acaba el programa.
  
  return 0; //Fin de main.

}
```

## Funciones
