# Actividad Integradora 1, Resaltador de sintaxis (evidencia de competencia)

![Tec](https://user-images.githubusercontent.com/72751268/165434642-3b658ea7-91cf-4a9a-82ef-83cb499e31b5.jpg)


## Instituto Tecnológico y de Estudios Superiores de Monterrey Campus Monterrey

### Implementación de métodos computacionales (TC2037.601)

#### Realizada por:

- Sergio Alejandro Esparza González - A01625430
- Eric Eugenio Oyervides Espino - A01570760
- Alejandro Mauricio Maqueo Huerta - A01620649

#### Semestre:

Febrero-junio 2022

#### Fecha:

26 de abril de 2022

## Nota importante
Todos los archivos de esta entrega deberán formar parte de una misma carpeta (no comprimida) para su correcta visualización.

## Descripción de la Evidencia

En equipos de 3 personas:

1. El Syntax Highlighter utilizará el output de su programa anterior: necesita una lista de tokens la cual va a procesar para poder determinar si el orden es correcto 
o no. 
2. Usando el lenguaje de su preferencia, implementen un analizador de sintaxis por método de descenso recursivo.
  - Pueden tener el ; como token final de cada expresión. Considerar cuando hay comentarios.
3. El programa debe convertir su entrada en documentos de HTML+CSS que resalten su léxico.
4. Incluye un README, donde venga la gramática de tu analizador. Y todo lo necesario para ejecutar tu código.
5. Utiliza unit testing e incluyelo en tu proyecto.
6. Utiliza las convenciones de codificación del lenguaje en el que está implementado tu programa (ej.; si estás usando Python, asegurate de seguir el PEP8).
7. Realiza un video donde se muestre el correcto funcionamiento de su programa.

Adicionalmente, habrá que hacer una reflexión de manera individual (un documento por miembro de equipo):
1. Reflexiona sobre la solución planteada, los algoritmos implementados y sobre el tiempo de ejecución de los mismos.
2. Calcula la complejidad de tu algoritmo
3. Plasma en un breve reporte de una página las conclusiones de tu reflexión en los puntos 1 y 2 de este inciso. Agrega además una breve reflexión sobre las 
implicaciones éticas que el tipo de tecnología que desarrollaste pudiera tener en la sociedad.

## Resolución

### Gramática

``` Bool

PAOC -> <Parentesis que abre> <Variable> <Parentesis que cierra> OP <Asignacion> M
  
AO -> <Variable> <Operador> <Asignacion> M
  
PA -> <Parentesis que abre> <Variable> <Parentesis que cierra> <Asignacion> M
  
A -> <Variable> <Asignacion> M
  
M -> T O C
  
C -> <Comentario>
  
C -> ɛ
  
T -> P
  
T -> <Entero>
  
T -> <Real>
  
T -> <Variable>
  
P -> <Parentesis que abre> M <Parentesis que cierra>
  
O -> OP M O
  
O -> ɛ
  
OP -> <Suma>
  
OP -> <Resta>
  
OP -> <Multiplicacion>
  
OP -> <Division>
  
OP -> <Potencia>
```

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

## main
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
### lexer
```C++

/*
Funcion que actua como el analizador lexico, separando las lineas de un archivo 
de texto en tokens y guardando en un vector todos los tokens de una linea con
su respectivo tipo.
Parametros: no.
Retorno: no.
*/
void lexer() {

  //Variables locales:
  vector<string> lineas; //Vector que guarda todas las lineas del archivo.
  string linea = ""; //Cadena que va almacenando cada linea del archivo.
  int cuenta = 0; //Cuenta de tokens en el archivo.
  string value = ""; //Cadena que lmacena todos los caracteres de un token.
  int index = 0; //Indice que va recorriendo cada linea del vector.
  char c = ' '; //Caracter que va almacenando cada caracter del vector de lineas.
  int nLinea = 0; //Iterador que recorre cada elemento del vector de lineas.
  int state = 0; //Entero que indica el estado en el que se encuentra el DFA.
  int past = 0; //Guarda el estado pasado.
  vector<string> grupo; //Guarda un token con su tipo respectivo.
  vector< vector<string> > grupos; //Guarda todos los tokens con su tipo 
    //respectivo.
  vector<int> cuentasLineas; //Cuenta de tokens por cada linea.
  vector< vector<string> > lineaGrupos; //Guarda los tokens con su tipo de una 
    //sola linea.
  int cuentaTokens = 0; //Cuenta los tokens que se han pasado al vector 
    //gruposLineas.

  //Dependiendo del numero de prueba:
  switch(nPrueba){ //Se abrira el archivo de prueba distinto:

    case 0: //En caso de la primera prueba...
      inFile.open("prueba0.txt"); //Abrir archivo "prueba.txt".
      break;
    
    case 1: //En caso de ser la segunda prueba...
      inFile.open("prueba1.txt"); //Abrir archivo "prueba1.txt".
      break;
    
    case 2: //Y asi sucesivamente...
      inFile.open("prueba2.txt");
      break;
    
    case 3:
      inFile.open("prueba3.txt");

  }    
  
  //Almacenar lineas del archivo:
  while (!inFile.eof()) { //Mientras el archivo no se acabe:

    getline(inFile, linea); //Se obtiene una de sus lineas.
    lineas.push_back(linea); //Se guarda en el vector lineas.
    
  }

  inFile.close(); //Cerrar archivo.

  for (int i = 0; i < lineas.size(); i++) { //Se recorre el vector con las lineas 
    //del archivo...

    for (int j = 0; j < lineas.at(i).length(); j ++) { //Se recorre cada linea...
      
      if (lineas.at(i)[j - 1] == ' ' && lineas.at(i)[j] != ' ') //Si el caracter 
        //pasado era un espacio, y el actual no es un espacio...
        cuenta++; //Tenemos un token mas.
      if (lineas.at(i)[j] == '/' && lineas.at(i)[j - 1] == '/') //Si el caracter 
        //anterior era un '/', y el caracter actual es un '/'...
        j = lineas.at(i).length(); //Nos salimos del ciclo para que no cuente
          //espacios en un comentario.
      
    }

    if (lineas.at(i)[0] != 0 && lineas.at(i)[0] != ' ') // Si el caracter inicial 
      //no es vacio o no es un espacio...
      cuenta++; //Se agrega otro token a la cuenta (el inicial).
    
    //cout << cuenta << endl;
    cuentasLineas.push_back(cuenta); //Se agrega la cuenta de tokens en la linea
      //al vector que guarda el numero de tokens de todas las lineas.
    
    cuenta = 0; //Se reinicia la cuenta para pasar a la siguiente linea.
    
  }

  /*
  printVector1D(cuentasLineas);
  cout << endl;
  */
  
  //Leer elementos del vector:
  while (nLinea < lineas.size()) { //Mientras haya lineas en el vector.
    
    do { //Hacer...

      past = state; //Se guarda el estado actual para que se mantenga como el 
        //anterior en esta iteracion.
      c = lineas.at(nLinea)[index]; //Se obtiene el caracter de una linea.
      index++; //Para pasar al siguiente caracter en la siguiente vuelta.
      state = transitionMatrix[state][filter(c)]; //Se obtiene el estado actual de
        //la matriz de transiciones segun c.
      
      if (state != 0) //Si es un estado diferente al inicial...
        value += c; //Se aniade el caracter actual al token.
      
      if (state == 0 && index > 0 && past != 0) { //Si se ha llegado a un espacio, 
        //sin que sea el inicio de la cadena o que el caracter anterior haya sido 
        //un espacio...

        grupo.push_back(value); //Se almacena el token obtenido en un vector 
          //unidimensional.
        grupo.push_back(getType(past)); //Se almacena el tipo del token, segun el 
          //estado pasado (antes de ser 0) en un vector unidimensional.
        grupos.push_back(grupo); //Se juntan el token y su tipo en un vector 
          //bidimensional.
        grupo.clear(); //Se limpia el vector unidimensional para almacenar otro 
          //token con su respectivo tipo.
        value = ""; //Se elimina el token obtenido para guardar otro token.
        
      }
      
      if (index == lineas.at(nLinea).length() && state != 0) { //Si se llego al 
        //final de la linea sin que el ultimo estado sea un espacio...
        
        grupo.push_back(value); //Se almacena el token obtenido en el vector 1D.
        grupo.push_back(getType(state)); //Se almacena en el vector 1D el tipo del 
          //token, segun el estado actual.
        
        grupos.push_back(grupo); //Se juntan el token y su tipo en un vector 2D.
        grupo.clear(); //Se limpia el vector 1D para almacenar otro token con su 
          //respectivo tipo.
        value = ""; //Se reinicia value para guardar otro token.
        
      }
  
    } while (index < lineas.at(nLinea).length()); //Mientras no se llegue al final 
      //de la linea. 

    nLinea++; //Vamos hacia la siguiente linea.
    index = 0; //El iterador debe de comenzar al inicio de la linea.
    value = ""; //Se reinicia el valor de value para guardar otro token.
    state = 0; //El estado del inicio de la linea es 0.
    past = 0; // El estado pasado sera 0 de igual forma.
    
  }

  /*
  //Formato de la salida:
  cout.width(80); //Se dejan 80 espacios para cada escritura, parecido a una 
    //tabla, pues 80 caracteres es una
  //convencion informal del maximo que debe de tener una linea de codigo.
  cout << left; //Se alinea la escritura a la izquierda de los espacios.
  cout << "Token"; //Los tokens estaran debajo de esta palabra.
  cout << "Tipo"; //Los tipos de tokens estaran debajo de esta palabra.
  cout << endl; //Se comenzaran a imprimir los elementos en la siguiente linea.

  printVector2D(grupos); //Imprimir la matriz de tokens y tipos respectivos.
  cout << endl;
  */

  //Almacenar grupos por lineas:
  for (int i = 0; i < cuentasLineas.size(); i++) { //Recorre el vector que tiene 
    //la cuenta de los tokens por linea...
    
    for (int j = 0; j < cuentasLineas.at(i); j++) { //Segun el numero de tokens 
      //que tenga la linea actual...
  
      lineaGrupos.push_back(grupos.at(j + cuentaTokens)); //Se almacenan los 
        //grupos en el vector lineaGrupos.
        
    }

    if (cuentasLineas.at(i) == 0) //Si el numero de tokens en esta linea es 0...
      gruposLineas.push_back({{"Linea en blanco"}}); //Se indica que esa linea 
        //esta "vacia".
    else //De otra forma...
      gruposLineas.push_back(lineaGrupos); //Se guardan los grupos de esa linea en 
        //gruposLineas.
    
    lineaGrupos.clear(); //Se volveran a contar los tokens de una linea.
    
    cuentaTokens += cuentasLineas.at(i); //La cuenta de tokens incrementa.
    
  }
  
}
```
### filter
```C++

/*
Funcion para obtener el numero de columna de la matriz de transiciones segun un 
determinado caracter.
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
  else if ((c >= 65 && c <= 68) || (c >= 70 && c <= 90) || (c >= 97 && c <= 100) ||
    (c >= 102 && c <= 122)) //Si es una letra que no sea 'e' o 'E':
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
```
### getType
```C++
/*
Funcion que obtiene el tipo de token segun un estado final del DFA.
Parametros: estado final (s) del DFA (int).
Retorno: tipo del token (string).
*/
string getType(int s) {

  switch(s) { //Segun sea el valor de s, se retorna el tipo del token 
    //correspondiente...

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
```
### parser
```C++

/*
Funcion que actua como el analizador sintactico, analizando el orden de los tokens 
en una linea, y a partir de ello, determinar si es correcto o incorrecto.
Parametros: no.
Retorno: no.
*/
void parser() {

  vector<string> tokensDeLinea; //Vector que guarda los tokens que hay en una
    //linea.
  bool resultado = true; //Booleano que indica si una linea tiene una sintaxis 
    //correcta.
  
  for (int i = 0; i < gruposLineas.size(); i++) { //Se recorre el vector de los 
    //grupos almacenados por linea...

    for (int j = 0; j < gruposLineas.at(i).size(); j++) { //Se recorre cada 
      //linea...

      if (gruposLineas.at(i).at(j).size() == 2) //Si en la linea actual si hay 
        //tokens...
        tokensDeLinea.push_back(gruposLineas.at(i).at(j).at(1)); //Se aniade a 
          //tokensDeLinea el token actual.
      else //Si no...
        tokensDeLinea.push_back(gruposLineas.at(i).at(j).at(0)); //Se aniade este 
          //elemento que corresponde cuando la linea esta vacia.
      
    }

    resultado = parseMenu(tokensDeLinea); //Se analiza tokensDeLinea 
      //sintacticamente, y se guarda el resultado (1 o 0).

    if (resultado && tokensDeLinea.size() == 0) //Si el resultado es correcto, y 
      //se analizaron todos los tokens de la linea...
      lineasCorrectas.push_back("Correcta"); //Se anade a el vector 
        //lineasCorrectas que esa linea tiene una estructura gramatical correcta.
    else //Si no...
      lineasCorrectas.push_back("Incorrecta"); //Se aniade que esta incorrecta.
    
    tokensDeLinea.clear(); //Se guardaran los tokens de otra linea en la vuelta 
      //siguiente.
    
  }

  cout << endl;
  
  //printVector1D(lineasCorrectas); //Se imprime el vector que indica si las lineas 
    //fueron correctas o no.
  
}
```
### parseMenu
```C++

/*
Funcion que verifica que los tokens de una linea tengan un orden correcto haciendo 
parsing con distintas opciones de la gramatica.
Parametros: vector (tL) por referencia con los tokens de una linea 
(vector<string>).
Retorno: valor Booleano (bool), verdadero si hay un correcto orden, de lo 
contrario, falso.
*/
bool parseMenu(vector<string>& tL) {
  
  vector<string> save = tL; //Vector de strings que guarda los tokens de la linea 
    //a como estaban inicialmente.

  if (tL.size() != 0 && tL.at(0) == "Linea en blanco") { //Si el vector de tokens 
    //no esta vacio, y tiene guardada la string "Linea en blanco"...
    
    tL.erase(tL.begin()); //Se borra el primer elemento del vector de tokens.
    return true; //El orden es correcto.

  }
  else { //Si no...
    
    if (parsePAO(tL)) //Se hace parsing con esta regla (asignacion con operador 
      //entre parentesis)...
      return true; //Si coincide, el orden es correcto.
    else
      tL = save; //Si no, se restablece el vector de tokens a su estado original.
    
    if (parseAO(tL)) //Se hace parsing con esta regla (asignacion con operador)...
      return true; //Si coincide, el orden es correcto.
    else
      tL = save; //Si no, se restablece el vector de tokens a su estado original.
    
    if (parsePA(tL)) //Se hace parsing con esta regla (asignacion entre 
      //parentesis)...
      return true; //Si coincide, el orden es correcto.
    else
      tL = save; //Si no, se restablece el vector de tokens a su estado original.
    
    if (parseA(tL)) //Se hace parsing con esta regla (asignacion simple)...
      return true; //Si coincide, el orden es correcto.
    else
      tL = save; //Si no, se restablece el vector de tokens a su estado original.
    
    if (parseM(tL)) //Se hace parsing con esta regla (expresion simple)...
      return true; //Si coincide, el orden es correcto.
    else
      tL = save; //Si no, se restablece el vector de tokens a su estado original.

    if (parseC(tL)) //Se hace parsing con esta regla (comentario)...
      return true; //Si coincide, el orden es correcto.
    else
      tL = save; //Si no, se restablece el vector de tokens a su estado original.
  
    return false; //Si no es ninguna de las opciones anteriores, no tiene un orden 
      //correcto.
    
  }
  
}
```
### parsePAO
```C++
/*
Funcion que verifica que los tokens de una linea tengan la estructura gramatical 
de asignacion con operador entre parentesis.
Parametros: vector (tL) por referencia con los tokens de una linea 
(vector<string>).
Retorno: valor Booleano (bool), verdadero si hay un correcto orden, de lo 
contrario, falso.
*/
bool parsePAO(vector<string>& tL) {

  if (tL.size() != 0 && tL.at(0) == "Parentesis que abre") //Si el primer token es 
    //un "Parentesis que abre"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else //Si no...
    return false; //No es este el orden.
  
  if (tL.size() != 0 && tL.at(0) == "Variable") //Si el primer token es una 
    //"Variable"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else //Si no...
    return false; //No es este el orden.

  if (tL.size() != 0 && tL.at(0) == "Parentesis que cierra") //Si el primer token 
    //es un "Parentesis que abre"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else //Si no...
    return false; //No es este el orden.

  if (!parseOP(tL)) //Si el primer token no es un operador aritmetico...
    return false; //No es este el orden.

  if (tL.size() != 0 && tL.at(0) == "Asignacion") //Si el primer token es un 
    //operador de "Asignacion"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else //Si no...
    return false; //No es este el orden.

  if (!parseM(tL)) //Si lo que sigue no es una expresion simple...
    return false; //No es este el orden.

  return true; //Si pasa todos los filtros, es este el orden.

}
```
### parseAO
```C++
/*
Funcion que verifica que los tokens de una linea tengan la estructura gramatical 
de asignacion con operador.
Parametros: vector (tL) por referencia con los tokens de una linea 
(vector<string>).
Retorno: valor Booleano (bool), verdadero si hay un correcto orden, de lo 
contrario, falso.
*/
bool parseAO(vector<string>& tL) {

  if (tL.size() != 0 && (tL.at(0) == "Variable")) //Si el primer token es una 
    //"Variable"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else
    return false; //Si no, este no es el orden.

  if (!parseOP(tL)) //Si el primer token no es un operador aritmetico...
    return false; //Este no es el orden.

  if (tL.size() != 0 && (tL.at(0) == "Asignacion")) //Si el primer token es un 
    //operador de "Asignacion"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else
    return false; //Si no, este no es el orden.

  if (!parseM(tL)) //Si lo que sigue no es una expresion simple...
    return false; //Este no es el orden.

  return true; //Si pasa todos los filtros, este es el orden.

}
```
### parsePA
```C++
/*
Funcion que verifica que los tokens de una linea tengan la estructura gramatical 
de asignacion entre parentesis.
Parametros: vector (tL) por referencia con los tokens de una linea 
(vector<string>).
Retorno: valor Booleano (bool), verdadero si hay un correcto orden, de lo 
contrario, falso.
*/
bool parsePA(vector<string>& tL) {

  if (tL.size() != 0 && (tL.at(0) == "Parentesis que abre")) //Si el primer token  
    //es un "Parentesis que abre"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else
    return false; //De lo contrario, no es este el orden.
  
  if (tL.size() != 0 && (tL.at(0) == "Variable")) //Si el primer token es una 
    //"Variable"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else
    return false; //De lo contrario, no es este el orden.

  if (tL.size() != 0 && (tL.at(0) == "Parentesis que cierra")) //Si el primer 
    //token es un "Parentesis que cierra"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else
    return false; //De lo contrario, no es este el orden.

  if (tL.size() != 0 && (tL.at(0) == "Asignacion")) //Si el primer token es un 
    //operador de "Asignacion"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else
    return false; //De lo contrario, no es este el orden.

  if (!parseM(tL)) //Si lo que sigue no es una expresion simple...
    return false; //No es este el orden.

  return true; //Si pasa todos los filtros, es este el orden.

}
```
### parseA
```C++
/*
Funcion que verifica que los tokens de una linea tengan la estructura gramatical 
de asignacion.
Parametros: vector (tL) por referencia con los tokens de una linea 
(vector<string>).
Retorno: valor Booleano (bool), verdadero si hay un correcto orden, de lo 
contrario, falso.
*/
bool parseA(vector<string>& tL) {
  
  if (tL.size() != 0 && (tL.at(0) == "Variable")) //Si el primer token es una 
    //"Variable"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else
    return false; //De lo contrario, no es este el orden.

  if (tL.size() != 0 && (tL.at(0) == "Asignacion")) //Si el primer token es un 
    //operador de "Asignacion"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else
    return false; //De lo contrario, no es este el orden.

  if (!parseM(tL)) //Si lo que sigue no es una expresion simple...
    return false; //No es este el orden.

  return true; //Si pasa todos los filtros, es este el orden.

}
```

### parseM
```C++
/*
Funcion que verifica que los tokens de una linea tengan la estructura gramatical 
de una expresion simple.
Parametros: vector (tL) por referencia con los tokens de una linea 
(vector<string>).
Retorno: valor Booleano (bool), verdadero si hay un correcto orden, de lo 
contrario, falso.
*/
bool parseM(vector<string>& tL) {

  if (!parseT(tL)) //Si lo que sigue no es una estructura terminal...
    return false; //Este no es el orden.

  if(!parseO(tL)) //Si lo que sigue no es una operacion (opcional)...
    return false; //Este no es el orden.

  if(!parseC(tL)) //Si lo que sigue no es un comentario (opcional)...
    return false; //Este no es el orden.

  return true; //Si pasa todos los filtros, este es el orden.
  
}
```

### parseC
```C++
/*
Funcion que verifica que el primer token de la linea enviada sea un comentario.
Parametros: vector (tL) por referencia con los tokens de una linea 
(vector<string>).
Retorno: valor Booleano (bool), verdadero si hay un correcto orden, de lo 
contrario, falso.
*/
bool parseC(vector<string>& tL) {

  if (tL.size() == 0 || tL.at(0) != "Comentario") //Si el primer token no es un 
    //comentario...
    return true; //Se retorna verdadero.
  else {
    
    tL.erase(tL.begin()); //De lo contrario, se elimina el primer token.
    return true; //Y se retorna verdadero.
    
  }
  
}
```
### parseOP
```C++
/*
Funcion que verifica que el primer token de la linea enviada sea un operador 
aritmetico.
Parametros: vector (tL) por referencia con los tokens de una linea 
(vector<string>).
Retorno: valor Booleano (bool), verdadero si hay un correcto orden, de lo 
contrario, falso.
*/
bool parseOP(vector<string>& tL) {

  if (tL.size() != 0 && (tL.at(0) == "Suma" || tL.at(0) == "Resta" || tL.at(0) == 
    "Multiplicacion" || tL.at(0) == "Division" || tL.at(0) == "Potencia")) { //Si 
      //la linea tieke tokens, y el primero es un operador aritmetico...
    
    tL.erase(tL.begin()); //Se borra el primer token.
    return true; //Se indica que asi fue.

  }
  else //Si no...
    return false; //No es el orden de la regla gramatical que llamo esta funcion.
  
}
```
### parseT
```C++
/*
Funcion que verifica que el/los primer/os token/s de una linea tenga/n la 
estructura gramatical de una estructura terminal.
Parametros: vector (tL) por referencia con los tokens de una linea 
(vector<string>).
Retorno: valor Booleano (bool), verdadero si hay un correcto orden, de lo 
contrario, falso.
*/
bool parseT(vector<string>& tL) {
  
  if (tL.size() != 0 && (tL.at(0) == "Entero" || tL.at(0) == "Real" || tL.at(0) == 
    "Variable")) { //Si el primer token es un elemento operable...

    tL.erase(tL.begin()); //Se borra el primer token.
    return true; //Se comprueba que es correcto.
    
  }
  else if (tL.size() != 0 && (tL.at(0) == "Parentesis que abre")) { //Si el primer 
    //token es un "Parentesis que abre"...

    if (!parseP(tL)) //Si lo que sigue no es una correcta estructura de 
      //parentesis...
      return false; //Se comprueba que es incorrecto.
    else //Si no...
      return true; //Se comprueba que es correcto.
    
  }
  else //Si no...
    return false; //Se comprueba que es incorrecto.

}
```
### parseP
```C++
/*
Funcion que verifica que el/los primer/os token/s de una linea tenga/n la 
estructura gramatical de una estructura de parentesis.
Parametros: vector (tL) por referencia con los tokens de una linea 
(vector<string>).
Retorno: valor Booleano (bool), verdadero si hay un correcto orden, de lo 
contrario, falso.
*/
bool parseP(vector<string>& tL) { 

  if (tL.size() != 0 && (tL.at(0) == "Parentesis que abre")) //Si el primer token 
    //es un "Parentesis que abre"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else
    return false; //Si no, la estructura no tiene un orden correcto.

  if(!parseM(tL)) //Si lo que sigue no es una expresion simple...
    return false; //El orden no es correcto.

  if (tL.size() != 0 && (tL.at(0) == "Parentesis que cierra")) //Si el primer 
    //token es un "Parentesis que cierra"...
    tL.erase(tL.begin()); //Se borra el primer token.
  else
    return false; //Si no, la estructura no tiene un orden correcto.

  return true; //Si pasa todos los filtros, es una estructura correcta.

}
```
### parseO
```C++
/*
Funcion que verifica que el/los primer/os token/s de una linea tenga/n la 
estructura gramatical de una operacion
Parametros: vector (tL) por referencia con los tokens de una linea 
(vector<string>).
Retorno: valor Booleano (bool), verdadero si hay un correcto orden, de lo 
contrario, falso.
*/
bool parseO(vector<string>& tL) {

  if (tL.size() == 0 || (tL.at(0) != "Suma" && tL.at(0) != "Resta" && tL.at(0) != 
    "Multiplicacion" && tL.at(0) != "Division" && tL.at(0) != "Potencia")) //Si el 
      //primer elemento no es un operador aritmetico...
    return true; //Es una estructura correcta (caso epsilon).
  else { //Si lo es...

    if (!parseOP(tL)) //Si el primer elemento no es un operador aritmetico...
      return false; //No es una estructura valida.

    if(!parseM(tL)) //Si lo que sigue no es una expresion simple...
      return false; //No es una estructura valida.

    if(!parseO(tL)) //Si lo que sigue no es una operacion...
      return false; //No es una estructura valida.

    return true; //Si pasa todos los filtros, es una estructura correcta.
    
  }
  
}
```
### printVector1D
```C++
/*
Funcion que imprime los elementos de un vector de strings.
Parametros: vector (v) de strings (vector<string>).
Retorno: nada.
*/
void printVector1D(vector<string> v) {

  for (int i = 0; i < v.size(); i++) { //Se recorre todo el vector...

    cout << "Linea " << i + 1 << ": " << v.at(i) << endl; //Se imrpime elemento 
      //por elemento.
    
  }
  
}
```
### printVector2D
```C++
/*
Funcion que imprime los elementos de una matriz de strings.
Parametros: matriz (v) de strings (vector< vector<string> >).
Retorno: nada.
*/
void printVector2D(vector< vector<string> > v) {

  //cout << endl; //Salto de linea para diferenciar los encabezados del resto de 
    //elementos de la tabla.
  
  for (int i = 0; i < v.size(); i++) { //Recorre cada vector de la matriz...

    //cout.width(79); //Se dejan 79 espacios para la salida (80 no por el espacio 
      //adicional entre elementos, ver for siguiente).
    //cout << left; //La salida se alinea a la izquierda.
    
    for (int j = 0; j < v.at(i).size(); j++) { //Recorre cada elemento de cada 
      //vector...

      cout << v.at(i).at(j) << " "; //Se imprime asi un elemento del vector mas un 
        //espacio por si llegara a ser de mas de 79 caracteres, que por lo menos
        //lo separe un espacio del siguiente elemento.
      
    }

    cout << endl; //Salto de linea para imprimir los elementos del siguiente 
      //vector de la matriz.
    
  }
  
}
```
### printVector3D
```C++
/*
Funcion que imprime los elementos de una matriz 3D de strings.
Parametros: matriz 3D (v) de strings (vector< vector< vector<string> > >).
Retorno: nada.
*/
void printVector3D(vector< vector< vector<string> > > v) {

  //Impresion con corchetes para separar vectores y matrices:
  for (int i = 0; i < v.size(); i++) { //Se recorre cada matriz 2D de la matriz
    //3D...

    for (int j = 0; j < v.at(i).size(); j++) { //Se recorre cada vector de la 
      //matriz 2D...

      if (j == 0) //Si es el primer vector...
        cout << "[ ";
      
      for (int k = 0; k < v.at(i).at(j).size(); k++) { //Se recorre cada elemento 
        //del vector...

        if (k == 0) //Si es el primer elemento...
          cout << "[ ";
        
        cout << v.at(i).at(j).at(k) << " ";

        if (k == v.at(i).at(j).size() - 1) //Si es el ultimo elemento...
          cout << "] ";
        
      }

      if (j == v.at(i).size() - 1) //Si es el ultimo vector...
        cout << "] ";
      
    }

    cout << endl;
    
  }

  //cout << "\nTamanio: " << v.size() << endl;
  //cout << endl;
  
}
```
### highlightHTML
```C++
/* 
Funcion que convierte el texto original a HTML y resalta la sintaxis de este.
Parametros: no.
Retorno: no.
*/
void highlightHTML() {

	//Hoja de estilos (CSS):
	outFile.open("Estilos.css");
	
	outFile << "body {" << endl; //Estilos del cuerpo:
		
		outFile << "\n\tbackground-color: black;" << endl; //Fondo negro.

	outFile << "\n}" << endl;
	
	outFile << "\np {" << endl; //Estilos del parrafo:
		
		outFile << "\n\tcolor: grey;" << endl; //Texto gris.

	outFile << "\n}" << endl;
	
	outFile << "\n.Suma {" << endl; //Clase Suma:
		
		outFile << "\n\tcolor: blue;" << endl; //Texto azul.

	outFile << "\n}" << endl;
	
	outFile << "\n.Resta {" << endl; //Clase Resta:
		
		outFile << "\n\tcolor: blue;" << endl; //Texto azul.

	outFile << "\n}" << endl;
	
	outFile << "\n.Multiplicacion {" << endl; //Clase Multiplicacion:
		
		outFile << "\n\tcolor: blue;" << endl; //Texto azul.

	outFile << "\n}" << endl;
	
	outFile << "\n.Division {" << endl; //Clase Division:
		
		outFile << "\n\tcolor: blue;" << endl; //Texto azul.

	outFile << "\n}" << endl;
	
	outFile << "\n.Potencia {" << endl; //Clase Potencia:
		
		outFile << "\n\tcolor: blue;" << endl; //Texto azul.

	outFile << "\n}" << endl;
	
	outFile << "\n.Asignacion {" << endl; //Clase Asignacion:
		
		outFile << "\n\tcolor: red;" << endl; //Texto rojo.

	outFile << "\n}" << endl;
	
	outFile << "\n.Parentesis{" << endl; //Clase Parentesis.
		
		outFile << "\n\tcolor: yellow;" << endl; //Texto amarillo.

	outFile << "\n}" << endl;
	
	outFile << "\n.que{" << endl; //Clase que:
		
		outFile << "\n\tcolor: yellow;" << endl; //Texto amarillo.

	outFile << "\n}" << endl;
	
	outFile << "\n.abre{" << endl; //Clase abre:
		
		outFile << "\n\tcolor: yellow;" << endl; //Texto amarillo.

	outFile << "\n}" << endl;
	
	outFile << "\n.cierra {" << endl; //Clase cierra:
		
		outFile << "\n\tcolor: yellow;" << endl; //Texto amarillo.

	outFile << "\n}" << endl;
	
	outFile << "\n.Entero {" << endl; //Clase Entero:
		
		outFile << "\n\tcolor: lime;" << endl; //Texto lima.

	outFile << "\n}" << endl;
	
	outFile << "\n.Real {" << endl; //Clase Real:
		
		outFile << "\n\tcolor: lime;" << endl; //Texto lima.

	outFile << "\n}" << endl;
	
	outFile << "\n.Variable {" << endl; //Clase Variable:
		
		outFile << "\n\tcolor: white;" << endl; //Texto blanco.

	outFile << "\n}" << endl;
	
	outFile << "\n.Comentario {" << endl; //Clase Comentario:
		
		outFile << "\n\tcolor: green;" << endl; //Color verde.

	outFile << "\n}";
	 
	outFile << "\n.Error {" << endl; //Estilos del cuerpo:

    outFile << "\n\tcolor: black;" << endl; //Letra negra.
		outFile << "\tbackground-color: rgb(157, 135, 135);" << endl; //Fondo 
      //blanquesino.

	outFile << "\n}" << endl;
	
	outFile.close();
	
	//Resaltador (HTML):
	outFile.open("Equipo5_Resaltado.html");
	
	outFile << "<!DOCTYPE html>" << endl; //Encabezado del documento.
	
		outFile << "\n\t<html>" << endl; //Inicio de la pagina.
	
    		outFile << "\n\t\t<head>" << endl; //Encabezado de la pagina:
    
    			outFile << "\n\t\t\t<title>Equipo05_Resaltador</title>" << endl; 
            //Titulo.
    			
				outFile << "\n\t\t\t<meta charset=\"UTF-8\">" << endl; //Charset.
    			outFile << "\t\t\t<meta name = \"viewport\" content=\"width = device-"
                  << "width, initial-scale = 1.0\">" << endl; //Viewport.
    
    			outFile << "\n\t\t\t<link rel=\"stylesheet\" href=\"Estilos.css\">" 
                  << endl; //Link al CSS.

    		outFile << "\n\t\t</head>" << endl;
    
    		outFile << "\n\t\t<body>" << endl; //Cuerpo:
        
    			outFile << "\n\t\t\t<p>" << endl; //Parrafo:
        
        	//A partir de aqui se automatiza:

          for (int i = 0; i < gruposLineas.size(); i++) { //Se recorre cada matriz 
            //2D de la matriz 3D...

            outFile << "\n\t\t\t\t" << i + 1 << " &nbsp&nbsp " << endl;
            
            for (int j = 0; j < gruposLineas.at(i).size(); j++) { //Se recorre 
              //cada vector de la matriz 2D...

              if (lineasCorrectas.at(i) == "Correcta") {
                
                if (gruposLineas.at(i).at(j).size() == 2) {
  
                  if (j != gruposLineas.at(i).size() - 1)
                  outFile << "\t\t\t\t<span class=\"" 
                          << gruposLineas.at(i).at(j).at(1)
                          << "\">" << gruposLineas.at(i).at(j).at(0) << " </span>" 
                          << endl;
                  else
                    outFile << "\t\t\t\t<span class=\"" 
                            << gruposLineas.at(i).at(j).at(1)
                            << "\">" << gruposLineas.at(i).at(j).at(0) 
                            << "</span>" << endl;
                  
                }

              }
              else{

                if (j == 0)
                  outFile << "\t\t\t\t<span class=\"Error\">";
                
                if (j != gruposLineas.at(i).size() - 1)
                  outFile << "<span class=\"" << gruposLineas.at(i).at(j).at(1)
                          << "\">" << gruposLineas.at(i).at(j).at(0) << " </span>";
                else
                    outFile << "<span class=\"" << gruposLineas.at(i).at(j).at(1)
                          << "\">" << gruposLineas.at(i).at(j).at(0) << "</span>";

                if (j == gruposLineas.at(i).size() - 1)
                  outFile << "</span>" << endl;
                
              }
            
            }

            outFile << "\t\t\t\t<br>" << endl;
    
          }
        
    			outFile << "\n\t\t\t</p>" << endl;
    
    		outFile << "\n\t\t</body>" << endl;

		outFile << "\n\t</html>";
	
	outFile.close();
  
}
```
### unitTesting
```C++
/*
Funcion: hacer unit testing del código, haciendo varias pruebas con el mismo.
Parametros: no.
Retorno: valor Booleano (bool): true si pasa todas las pruebas, false si no.
*/
bool unitTesting() {

  int pruebasPasadas = 0; //Pruebas que pasa el codigo.
  string trial0[] = {"Correcta", "Correcta", "Incorrecta", "Incorrecta",
    "Correcta", "Correcta", "Correcta"}; //Primera prueba.
  string trial1[] = {"Correcta", "Incorrecta", "Correcta", "Incorrecta",
    "Correcta", "Correcta", "Incorrecta", "Correcta", "Correcta", "Incorrecta",
    "Incorrecta", "Correcta", "Correcta", "Correcta"}; //Segunda prueba.
  string trial2[] = {"Correcta", "Correcta", "Correcta", "Incorrecta",
    "Incorrecta", "Correcta", "Incorrecta", "Correcta", "Correcta",
    "Correcta", "Correcta", "Incorrecta", "Correcta", "Incorrecta", "Correcta",
    "Correcta", "Correcta", "Correcta", "Correcta", "Incorrecta", "Correcta"};
    //Prueba 3.
  string trial3[] = {"Correcta", "Correcta", "Correcta", "Correcta",
    "Correcta", "Incorrecta", "Correcta", "Correcta", "Correcta", "Incorrecta",
    "Correcta", "Correcta", "Correcta", "Incorrecta", "Correcta", "Correcta",
    "Incorrecta", "Correcta", "Correcta", "Correcta", "Correcta", "Correcta",
    "Correcta", "Incorrecta", "Correcta", "Incorrecta", "Correcta",
    "Correcta"}; //Prueba 4
  
  //4 pruebas:
  for (int i = 0; i < 4; i++) {
    
    gruposLineas.clear(); //Se limpia el vector que guarda los tokens y tipos 
      //linea por linea.
    lineasCorrectas.clear(); //Se limpia el vector que indica si la gramatica
      //de una linea es correcta o no.

    lexer();
    parser();

    //Validar resultados:
    switch(nPrueba) { 

      case 0: //Primera prueba.
        
        if (!match(trial0)){ //Si no pasa la primera prueba...
          
          cout << "Prueba 1/4 fallida. Revisar programa." << endl;
          return false; //El programa esta incorrecto.
          
        }
        else{ //Si la pasa.
          
          pruebasPasadas++; //Se incrementa el numero de pruebas pasadas.
          cout << "Prueba 1/4 pasada correctamente." << endl;
          
        }
        break;
      
      case 1:

        if (!match(trial1)){ //Si no pasa la segunda prueba...
          
          cout << "Prueba 2/4 fallida. Revisar programa." << endl;
          return false; //El programa esta incorrecto.
          
        }
        else{ //Si la pasa.
          
          pruebasPasadas++; //Se incrementa el numero de pruebas pasadas.
          cout << "Prueba 2/4 pasada correctamente." << endl;
          
        }   
        
        break;

      case 2:
        
        if (!match(trial2)){ //Si no pasa la tercera prueba...
          
          cout << "Prueba 3/4 fallida. Revisar programa." << endl;
          return false; //El programa esta incorrecto.
          
        }
        else{ //Si la pasa.
          
          pruebasPasadas++; //Se incrementa el numero de pruebas pasadas.
          cout << "Prueba 3/4 pasada correctamente." << endl;
          
        }
        
        break;
      
      case 3:

        if (!match(trial3)){ //Si no pasa la primera prueba...
          
          cout << "Prueba 4/4 fallida. Revisar programa." << endl;
          return false; //El programa esta incorrecto.
          
        }
        else{ //Si la pasa.
          
          pruebasPasadas++; //Se incrementa el numero de pruebas pasadas.
          cout << "Prueba 4/4 pasada correctamente." << endl;
          
        }   
      
    }

    nPrueba++;
    
  }

  return true; //Si paso todas las pruebas, el programa es correcto.

}
```
### match
```C++
/*
Funcion que compara los resultados esperados de una prueba con los resultados reales.
Parametros: arreglo (trial) de strings (string[]) con los resultados esperados.
Retorno: valor Booleano (bool), devuelve true si coincidieron todos los resultados, 
false si no.
*/
bool match(string trial[]) {

  for (int i = 0; i < lineasCorrectas.size(); i++) { //Se recorre el vector
    //"lineasCorrectas"...

    if (lineasCorrectas.at(i) != trial[i]) //Si una linea no dio de
      //resultado lo que se esperaba...
      return false; //No se pasa la prueba.
    
  }

  return true; //Pasa la prueba al haber comprobado linea por linea.
  
}
```

#### Pruebas

Utilizando el ejemplo del inicio (en el archivo "prueba0.txt"):
```Bool

( a ) = 2
( 8 +  7 - ( 8 * 9 ) - 20 )
( 8 - ( 4 )
3 + - 2
3 + ( -2 )
3 + -2
1
					     
```
					     
Utilizando el ejemplo del inicio (en el archivo "prueba1.txt"):
```Bool

( 5 )
(5)

((5))
( ( 5 ) )

(((6)))
( ( ( 6 ) ) )

( 6)
(6 )

( 5 + 6 )
5 + 6
					     
```
					     
Utilizando el ejemplo del inicio (en el archivo "prueba2.txt"):
```Bool

a = 5

b + = 6
a+ = 7
a += 5

b =+ 8
b + = 9

//Com

/ com

Not Com

Alejandro
Eric
Alex

( + - )
( 5 + 6 - 8 )
					     
```
					     
Utilizando el ejemplo del inicio (en el archivo "prueba3.txt"):
```Bool

b = 7
5

6 + 8

7+

a + = 6

2+ 5

( a ) = 7 + 9

( b + 5 ) = 5

b = 7 + ( ( 5 + 8 ) )
b = 7 + ( ( 5 + 8 )

a = 32.4 * ( -8.6 - b ) / 6.1E-8

d = a ^ b // Esto es un comentario

//Hola
/ hola

a = //

a = 5 //
					     
```					     


Al ejecutar el programa, se muestra lo siguiente:

![Output](https://user-images.githubusercontent.com/72751268/165436819-21b00107-5e1e-4be8-8f46-95e6fc86af1b.JPG)

Obteniendo así los resultados deseados.

## Nota para el docente

Para más casos de prueba, se recomienda modificar directamente los archivos de la serie "pruebaN.txt" para no tener que modificar el código. Solo que el output directo es del último.
