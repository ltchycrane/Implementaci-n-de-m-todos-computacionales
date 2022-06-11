# Actividad Integradora 2, Resaltador de sintaxis paralelo (evidencia de competencia)


## Instituto Tecnológico y de Estudios Superiores de Monterrey Campus Monterrey

### Implementación de métodos computacionales (TC2037.601)

![Tec](https://user-images.githubusercontent.com/72751268/165434642-3b658ea7-91cf-4a9a-82ef-83cb499e31b5.jpg)

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

1. Extiende el programa realizado en la Actividad Integradora para que aplique, de manera secuencial, el resaltado de léxico a múltiples archivos fuente contenidos en uno o varios directorios anidados.
  1. Tienes que buscar todos los archivos existentes en el path provisto.
 
2. Desarrolla una nueva versión de tu programa para que realice el proceso de resaltado de léxico de manera paralela con el fin de aprovechar los múltiples núcleos disponibles en los CPUs modernos (usando multiprocessing o multithreading).
  1. Recolecta información de la ejecución de cada proceso/thread (numero de proceso/thread, tiempo de ejecución, etc).
 
3. Asegúrate de utilizar en tu código fuente las convenciones de codificación del lenguaje en el que está implementado tu programa.

4. Mide los tiempos de varias ejecuciones de las dos versiones de tu programa. Calcula el speedup obtenido. Reflexiona sobre las soluciones planteadas, los algoritmos implementados y sobre el tiempo de ejecución de estos.

5. Calcula la complejidad de tu algoritmo basada en el número de iteraciones y contrástala con el tiempo obtenido en el punto 4.

6. Plasma en un breve reporte de una página las conclusiones de tu reflexión en los puntos 5 y 6. Agrega además una breve reflexión sobre las implicaciones éticas que el tipo de tecnología que desarrollaste pudiera tener en la sociedad.

Para esta actividad se sugiere utilizar la técnica didáctica POL (Aprendizaje Basado en Proyectos, por sus siglas en inglés).

Realiza un video donde expliques tu procedimiento y muestres el correcto funcionamiento

### Codigo

El lenguaje utilizado es C++; el código se encuentra escrito en el archivo "main.cpp". Para correr el programa se necesita un compilador que soporte C++11 o versiones posteriores, tal como GCC 4.3 o una versión posterior, y un IDE que use este compilador, como DEV C++.

La descripción más precisa del código se encuentra en los comentarios del mismo, incluyendo los encabezados de las funciones, sin embargo, aquí se plasma a grandes rasgos qué es lo que hace en orden.

### Concurrencia

Para la concurrencia utilizamos threads para hacer cada uno de los procesos, tuvimos que juntar todos los procesos en la función "logica" y corrimos la misma función en cada thread. Cuando el thread acaba (con thread.join()), sigue con el siguiente. Ya que nadamás hacemos un proceso a la vez, no necesitamos tener más de 1 thread activo.  
``` cpp


//*******************************************************************************
// Actividad Integradora 2, Resaltador de sintaxis paralelo (evidencia de
// competencia)
// Programa que recibe como entrada un directorio con varios archivos con una
// serie de expresiones aritmeticas, escritas bajo ciertas reglas, y entrega como
// salida documentos de HTML+CSS que resalten ese lexico.
// Creado por: Sergio Alejandro Esparza Gonzalez - A01625430, Alejandro Mauricio
// Maqueo Huerta - A01620649, Eric Eugenio Oyervides Espino - A01570760.
// Ultima modificacion realizada el: 10 de junio de 2022.
//*******************************************************************************

//Librerias:
#include <iostream>     //Flujo de datos.
#include <vector>       //Vectores.
#include <fstream>      //Flujo de archivos.
#include <string>       //Cadenas.
#include <filesystem>   //Trabajar con paths y directorios.
#include <thread>       //Threads.
#include <chrono>       //Contador de tiempo.
#include <unistd.h>     //Tiempo de espera.

//Espacios de nombres:
using namespace std; //Estandar.
using namespace filesystem; //Sistema de archivos.
using namespace chrono; //Tiempo.

//Prototipos de funciones:
void lexer(string); //Analizador lexico.
int filter(char); //Obtener numero de columna de la matriz de transiciones.
string getType(int); //Obtener el tipo de un token.
void printVector1D(vector<string>); //Imprimir elementos de un vector de
  //cadenas de 1 dimension.
void printVector2D(vector< vector<string> >); //Imprimir elementos de un
  //vector de cadenas de 2 dimensiones.
void printVector3D(vector< vector< vector<string> > >); //Imprimir elementos
  //de un vector de cadenas de 3 dimensiones.
void parser(vector< vector< vector<string> > >, string); //Analizador sintactico.
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
void highlightHTML(vector<string>, vector< vector< vector<string> > >, string);
    //Resaltador de sintaxis.
bool match(string[]); //Hacer match de resultados reales y esperados.
void logica(string); //Funcion base.

/*
Funcion principal.
Parametros: no.
Retorno: 0 (int):
*/
int main() {

    vector<string> txts; //Vector que guarda los nombres de los archivos de texto
    //encontrados.

    string path = "\ActIntegradora2"; //Directorio principal en el que estaran
        //los archivos de texto.

    //Encontrar todos los archivos con extension ".txt":
    for (const auto & file : recursive_directory_iterator(path)) {

        if (file.path().extension() == ".txt")
            txts.push_back(file.path().string()); //Guardar los nombres en el
                //vector.

    }

    //cout << "El directorio (y subdirectorios) contiene en total: "
         //<< txts.size() << " archivos con extension \".txt\"" << endl;

    //Son 10 archivos TXT contados.
    thread hilo0{logica, txts[0]}; //Llamar funcion base del programa dentro de
                             //un thread.
    hilo0.join();  //Esperar que el thread termine para pasar a la siguiente
                   //linea de codigo.

    thread hilo1{logica, txts[1]};
    hilo1.join();

    thread hilo2{logica, txts[2]};
    hilo2.join();

    thread hilo3{logica, txts[3]};
    hilo3.join();

    thread hilo4{logica, txts[4]};
    hilo4.join();

    thread hilo5{logica, txts[5]};
    hilo5.join();

    thread hilo6{logica, txts[6]};
    hilo6.join();

    thread hilo7{logica, txts[7]};
    hilo7.join();

    thread hilo8{logica, txts[8]};
    hilo8.join();

    thread hilo9{logica, txts[9]};
    hilo9.join();

  return 0; //Fin de main.

}

/* Funcion base del programa.
Parametros: no.
Retorno: no. */
void logica(string archivo) {

    steady_clock::time_point tiempoInicial = steady_clock::now();
    cout << "\nID del proceso: " << this_thread::get_id() << endl;
    cout << "Archivo de entrada: " << archivo << endl;
    lexer(archivo);
    steady_clock::time_point tiempoFinal = steady_clock::now();
    duration<double> tiempoTranscurrido = duration_cast<duration<double>>(tiempoFinal - tiempoInicial);
    cout << "Tiempo transcurrido: " << tiempoTranscurrido.count() << " segundos." << endl;

}

/*
Funcion que actua como el analizador lexico, separando las lineas de un archivo
de texto en tokens y guardando en un vector todos los tokens de una linea con
su respectivo tipo.
Parametros: nombre del archivo a analizar (string).
Retorno: no.
*/
void lexer(string archivo) {

    //Constantes:
    const int ERROR = 18; //Estado de error.

  //Variables locales:

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
  vector< vector< vector<string> > > gruposLineas; //Guarda los tokens y tipos
  //linea por linea.

  inFile.open(archivo); //Abrir archivo de entrada
    //correspondiente al numero de proceso.

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

    cuentasLineas.push_back(cuenta); //Se agrega la cuenta de tokens en la linea
      //al vector que guarda el numero de tokens de todas las lineas.

    cuenta = 0; //Se reinicia la cuenta para pasar a la siguiente linea.

  }

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

  string base_filename = archivo.substr(archivo.find_last_of("/\\") + 1);
  string::size_type const p(base_filename.find_last_of('.'));
  string archivoNombre = base_filename.substr(0, p);

  parser(gruposLineas, archivoNombre);

}

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

/*
Funcion que actua como el analizador sintactico, analizando el orden de los tokens
en una linea, y a partir de ello, determinar si es correcto o incorrecto.
Parametros: matriz 3D de grupos por lineas (vector< vector< vector<string> > >), y
nombre del archivo de salida (string).
Retorno: no.
*/
void parser(vector< vector< vector<string> > > gruposLineas, string archivoNombre) {


  vector<string> lineasCorrectas; //Indica si la gramatica de una linea es
  //correcta o no.
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

    highlightHTML(lineasCorrectas, gruposLineas, archivoNombre); //Se usa el resaltador de sintaxis.

}

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

/*
Funcion que imprime los elementos de un vector de strings.
Parametros: vector (v) de strings (vector<string>).
Retorno: nada.

void printVector1D(vector<string> v) {

  for (int i = 0; i < v.size(); i++) { //Se recorre todo el vector...

    cout << "Linea " << i + 1 << ": " << v.at(i) << endl; //Se imrpime elemento
      //por elemento.

  }

}
*/

/*
Funcion que imprime los elementos de una matriz de strings.
Parametros: matriz (v) de strings (vector< vector<string> >).
Retorno: nada.

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
*/

/*
Funcion que imprime los elementos de una matriz 3D de strings.
Parametros: matriz 3D (v) de strings (vector< vector< vector<string> > >).
Retorno: nada.

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
*/

/*
Funcion que convierte el texto original a HTML y resalta la sintaxis de este.
Parametros: vector que indica si las lineas del archivo son correctas
(vector<string>), matriz 3D con los grupos de cada linea
(vector< vector< vector<string> > >), y el nombre del archivo de salida
(string).
Retorno: no.
*/
void highlightHTML(vector<string> lineasCorrectas, vector< vector< vector<string> > > gruposLineas, string archivoNombre) {

    //Variables globales:
    ifstream inFile; //Leer datos de un archivo.
    ofstream outFile; //Escribir datos en un archivo.

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

    cout << "Archivo de salida: " << archivoNombre << ".html" << endl;

	outFile.open(archivoNombre + ".html"); //Abrir
        //archivo de salida con el mismo nombre al de entrada (pero HTML).

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

    this_thread::sleep_for(milliseconds(64));

}

```
### Paralelismo

``` cpp
//*****************************************************************************
// Actividad Integradora 2, Resaltador de sintaxis paralelo (evidencia de
// competencia)
// Programa que recibe como entrada un directorio con varios archivos con una
// serie de expresiones aritmeticas, escritas bajo ciertas reglas, y entrega
// como salida documentos de HTML+CSS que resalten ese lexico.
// Creado por: Sergio Alejandro Esparza Gonzalez - A01625430, Alejandro
// Mauricio Maqueo Huerta - A01620649, Eric Eugenio Oyervides Espino -
// A01570760.
// Ultima modificacion realizada el: 10 de junio de 2022.
//*****************************************************************************

//Librerias:
#include <iostream>     //Flujo de datos.
#include <vector>       //Vectores.
#include <fstream>      //Flujo de archivos.
#include <string>       //Cadenas.
#include <filesystem>   //Trabajar con paths y directorios.
#include <thread>       //Threads.
#include <chrono>       //Contador de tiempo.
#include <unistd.h>     //Tiempo de espera.

//Espacios de nombres:
using namespace std; //Estandar.
using namespace filesystem; //Sistema de archivos.
using namespace chrono; //Tiempo.

//Prototipos de funciones:
void lexer(string); //Analizador lexico.
int filter(char); //Obtener numero de columna de la matriz de transiciones.
string getType(int); //Obtener el tipo de un token.
void printVector1D(vector<string>); //Imprimir elementos de un vector de
  //cadenas de 1 dimension.
void printVector2D(vector< vector<string> >); //Imprimir elementos de un
  //vector de cadenas de 2 dimensiones.
void printVector3D(vector< vector< vector<string> > >); //Imprimir elementos
  //de un vector de cadenas de 3 dimensiones.
void parser(vector< vector< vector<string> > >, string); //Analizador
    //sintactico.
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
void highlightHTML(vector<string>, vector< vector< vector<string> > >, string);
    //Resaltador de sintaxis.
bool match(string[]); //Hacer match de resultados reales y esperados.
void logica(string); //Funcion base.

/*
Funcion principal.
Parametros: no.
Retorno: 0 (int):
*/
int main() {

    vector<string> txts; //Vector que guarda los nombres de los archivos de
    //texto encontrados.

    string path = "\ActIntegradora2"; //Directorio principal en el que estaran
        //los archivos de texto.

    //Encontrar todos los archivos con extension ".txt":
    for (const auto & file : recursive_directory_iterator(path)) {

        if (file.path().extension() == ".txt")
            txts.push_back(file.path().string()); //Guardar los nombres en el
                //vector.

    }

    //cout << "El directorio (y subdirectorios) contiene en total: "
         //<< txts.size() << " archivos con extension \".txt\"" << endl;

    //Son 10 archivos TXT contados.
    thread hilo0{logica, txts[0]}; //Llamar funcion base del programa dentro
                             //de un thread.
    thread hilo1{logica, txts[1]};
    thread hilo2{logica, txts[2]};
    thread hilo3{logica, txts[3]};
    thread hilo4{logica, txts[4]};
    thread hilo5{logica, txts[5]};
    thread hilo6{logica, txts[6]};
    thread hilo7{logica, txts[7]};
    thread hilo8{logica, txts[8]};
    thread hilo9{logica, txts[9]};

    hilo0.join();
    hilo1.join();
    hilo2.join();
    hilo3.join();
    hilo4.join();
    hilo5.join();
    hilo6.join();
    hilo7.join();
    hilo8.join();
    hilo9.join();

  return EXIT_SUCCESS; //Fin de main.

}

/*
Funcion base del programa.
Parametros: nombre del archivo a analizar (string).
Retorno: no.
*/
void logica(string archivo) {

    steady_clock::time_point tiempoInicial = steady_clock::now();
    cout << "\nID del proceso: " << this_thread::get_id() << endl;
    cout << "Archivo de entrada: " << archivo << endl;
    cout << "Dentro de: " << archivo << endl;
    lexer(archivo);
    steady_clock::time_point tiempoFinal = steady_clock::now();
    duration<double> tiempoTranscurrido = duration_cast<duration<double
        >>(tiempoFinal - tiempoInicial);
    cout << "Tiempo transcurrido: " << tiempoTranscurrido.count()
        << " segundos." << endl;

}

/*
Funcion que actua como el analizador lexico, separando las lineas de un archivo
de texto en tokens y guardando en un vector todos los tokens de una linea con
su respectivo tipo.
Parametros: nombre del archivo a analizar (string).
Retorno: no.
*/
void lexer(string archivo) {

    //Constantes:
    const int ERROR = 18; //Estado de error.

  //Variables locales:

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

  vector<string> lineas; //Vector que guarda todas las lineas del archivo.
  string linea = ""; //Cadena que va almacenando cada linea del archivo.
  int cuenta = 0; //Cuenta de tokens en el archivo.
  string value = ""; //Cadena que lmacena todos los caracteres de un token.
  int index = 0; //Indice que va recorriendo cada linea del vector.
  char c = ' '; //Caracter que va almacenando cada caracter del vector de
    //lineas.
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
  vector< vector< vector<string> > > gruposLineas; //Guarda los tokens y tipos
  //linea por linea.

  inFile.open(archivo); //Abrir archivo de entrada
    //correspondiente al numero de proceso.

  //Almacenar lineas del archivo:
  while (!inFile.eof()) { //Mientras el archivo no se acabe:

    getline(inFile, linea); //Se obtiene una de sus lineas.
    lineas.push_back(linea); //Se guarda en el vector lineas.

  }

  inFile.close(); //Cerrar archivo.

  for (int i = 0; i < lineas.size(); i++) { //Se recorre el vector con las
    //lineas del archivo...

    for (int j = 0; j < lineas.at(i).length(); j ++) { //Se recorre cada
                                                       //linea...

      if (lineas.at(i)[j - 1] == ' ' && lineas.at(i)[j] != ' ') //Si el
        //caracter pasado era un espacio, y el actual no es un espacio...
        cuenta++; //Tenemos un token mas.
      if (lineas.at(i)[j] == '/' && lineas.at(i)[j - 1] == '/') //Si el
        //caracter anterior era un '/', y el caracter actual es un '/'...
        j = lineas.at(i).length(); //Nos salimos del ciclo para que no cuente
          //espacios en un comentario.

    }

    if (lineas.at(i)[0] != 0 && lineas.at(i)[0] != ' ') // Si el caracter
      //inicial no es vacio o no es un espacio...
      cuenta++; //Se agrega otro token a la cuenta (el inicial).

    cuentasLineas.push_back(cuenta); //Se agrega la cuenta de tokens en la
      //linea al vector que guarda el numero de tokens de todas las lineas.

    cuenta = 0; //Se reinicia la cuenta para pasar a la siguiente linea.

  }

  //Leer elementos del vector:
  while (nLinea < lineas.size()) { //Mientras haya lineas en el vector.

    do { //Hacer...

      past = state; //Se guarda el estado actual para que se mantenga como el
        //anterior en esta iteracion.
      c = lineas.at(nLinea)[index]; //Se obtiene el caracter de una linea.
      index++; //Para pasar al siguiente caracter en la siguiente vuelta.
      state = transitionMatrix[state][filter(c)]; //Se obtiene el estado actual
        //de la matriz de transiciones segun c.

      if (state != 0) //Si es un estado diferente al inicial...
        value += c; //Se aniade el caracter actual al token.

      if (state == 0 && index > 0 && past != 0) { //Si se ha llegado a un
        //espacio, sin que sea el inicio de la cadena o que el caracter
        //anterior haya sido un espacio...

        grupo.push_back(value); //Se almacena el token obtenido en un vector
          //unidimensional.
        grupo.push_back(getType(past)); //Se almacena el tipo del token, segun
          //el estado pasado (antes de ser 0) en un vector unidimensional.
        grupos.push_back(grupo); //Se juntan el token y su tipo en un vector
          //bidimensional.
        grupo.clear(); //Se limpia el vector unidimensional para almacenar otro
          //token con su respectivo tipo.
        value = ""; //Se elimina el token obtenido para guardar otro token.

      }

      if (index == lineas.at(nLinea).length() && state != 0) { //Si se llego al
        //final de la linea sin que el ultimo estado sea un espacio...

        grupo.push_back(value); //Se almacena el token obtenido en el vector 1D.
        grupo.push_back(getType(state)); //Se almacena en el vector 1D el tipo
          //del token, segun el estado actual.

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

  string base_filename = archivo.substr(archivo.find_last_of("/\\") + 1);
  string::size_type const p(base_filename.find_last_of('.'));
  string archivoNombre = base_filename.substr(0, p);

  parser(gruposLineas, archivoNombre);

}

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

/*
Funcion que actua como el analizador sintactico, analizando el orden de los tokens
en una linea, y a partir de ello, determinar si es correcto o incorrecto.
Parametros: matriz 3D de grupos por lineas (vector< vector< vector<string> > >), y
nombre del archivo de salida (string).
Retorno: no.
*/
void parser(vector< vector< vector<string> > > gruposLineas, string archivoNombre) {


  vector<string> lineasCorrectas; //Indica si la gramatica de una linea es
  //correcta o no.
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

    highlightHTML(lineasCorrectas, gruposLineas, archivoNombre); //Se usa el resaltador de sintaxis.

}

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

/*
Funcion que imprime los elementos de un vector de strings.
Parametros: vector (v) de strings (vector<string>).
Retorno: nada.

void printVector1D(vector<string> v) {

  for (int i = 0; i < v.size(); i++) { //Se recorre todo el vector...

    cout << "Linea " << i + 1 << ": " << v.at(i) << endl; //Se imrpime elemento
      //por elemento.

  }

}
*/

/*
Funcion que imprime los elementos de una matriz de strings.
Parametros: matriz (v) de strings (vector< vector<string> >).
Retorno: nada.

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
*/

/*
Funcion que imprime los elementos de una matriz 3D de strings.
Parametros: matriz 3D (v) de strings (vector< vector< vector<string> > >).
Retorno: nada.

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
*/

/*
Funcion que convierte el texto original a HTML y resalta la sintaxis de este.
Parametros: vector que indica si las lineas del archivo son correctas
(vector<string>), matriz 3D con los grupos de cada linea
(vector< vector< vector<string> > >), y el nombre del archivo de salida
(string).
Retorno: no.
*/
void highlightHTML(vector<string> lineasCorrectas, vector< vector< vector<string> > > gruposLineas, string archivoNombre) {

    //Variables globales:
    ifstream inFile; //Leer datos de un archivo.
    ofstream outFile; //Escribir datos en un archivo.

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

    cout << "Imprimo: " << archivoNombre << endl;

	outFile.open(archivoNombre + ".html"); //Abrir
        //archivo de salida con el mismo nombre al de entrada (pero HTML).

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

    this_thread::sleep_for(milliseconds(64));

}

```





