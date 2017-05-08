 
%{

  #include <stdio.h>
  extern FILE *yyin;
  extern int yylex();

  #define YYDEBUG 1
int yylex();
int yyerror(char *);
%}

%token BOOLEANO BUCLE CARACTER CASOS COMIENZO CONSTANTE CUANDO DE DEVOLVER
%token EN ENTERO ENTONCES ES EXPORTAR DECLARACION DESCENDENTE FALSO FIN
%token FUNCION MIENTRAS MODULO OTRO PARA PROCEDIMIENTO RANGO REAL REFERENCIA
%token REGISTRO REPETIR SALIR SI SINO SUBTIPO TABLA TIPO VACIA VALOR
%token VERDADERO CTC_CARACTER CTC_CADENA IDENTIFICADOR CTC_ENTERA CTC_REAL
%token NOMBRE_MODULO PUNTOS RESTO ASIGNACION FLECHA POTENCIA INC DEC DESP_IZDA
%token DESP_DCHA LEQ GEQ NEQ AND OR  

%%

/************/
/* programa */
/************/

programa
    : modulos exportaciones declaraciones_subprograma   { printf ("EXITO: programa -> export decls_subpr\n"); }
    ;

modulos
    :                  { printf ("  modulos -> epsilon\n"); }
    | modulos modulo   { printf ("  modulos -> modulos modulo\n"); }
    ;

modulo
    : MODULO NOMBRE_MODULO ';'   { printf ("  modulo -> MODULO NOMBRE_MODULO ';'\n"); }
    ;

exportaciones
    :                                { printf ("  export -> epsilon\n"); }
    | EXPORTAR identificadores ';'   { printf ("  export -> EXPORTAR ids ';'\n"); }
    ;

identificadores
    : IDENTIFICADOR                       { printf ("  ids -> ID\n"); }
    | identificadores ',' IDENTIFICADOR   { printf ("  ids -> id ',' ID\n"); }
    ;

/*******************************/
/* declaracion de subprogramas */
/*******************************/

declaraciones_subprograma: declaracion_subprograma { printf ("  declaraciones_subprograma -> declaracion_subprograma\n"); }
    | declaraciones_subprograma declaracion_subprograma { printf ("  declaraciones_subprograma -> declaraciones_subprograma declaracion_subprograma\n"); }
    ;

declaracion_subprograma: especificacion_subprograma cuerpo_subprograma ';' {printf(" declaracion_subprograma -> especificacion_subprograma ',' cuerpo_subprograma\n");}
    ;
 
especificacion_subprograma: PROCEDIMIENTO IDENTIFICADOR parametrizacion  {printf(" especificacion_subprograma -> PROCEDIMIENTO ',' IDENTIFICADOR ',' parametrizacion\n");}
    | FUNCION IDENTIFICADOR parametrizacion DEVOLVER especificacion_tipo {printf(" especificacion_subprograma -> FUNCION ',' IDENTIFICADOR ',' parametrizacion ',' devolver ',' especificacion_tipo\n");}
    ;

parametrizacion: { printf ("  parametrizacion -> epsilon\n"); }
    | '(' lista_declaracion_parametros ')'       {printf(" parametrizacion -> '(' lista_declaracion_parametros ')'\n");}
    ;

lista_declaracion_parametros: declaracion_parametros {printf(" lista_declaracion_parametros -> epsilon\n");}
    | lista_declaracion_parametros ';' declaracion_parametros { printf ("  lista_declaracion_parametros -> lista_declaracion_parametros ';' declaracion_parametros\n"); }
    ;

declaracion_parametros
    : identificadores ':' modo especificacion_tipo {printf("declaracion_parametros -> identificadores ',' modo ',' especificacion_tipo\n");}
    ;

modo 
    : VALOR { printf(" modo -> VALOR\n");}
    | REFERENCIA {printf(" modo -> REFERENCIA\n");}
    ;

especificacion_tipo
    : tipo_compuesto { printf(" especificacion_tipo -> tipo_compuesto\n");}
    | nombre_tipo {printf(" especificacion_tipo -> nombre_tipo\n");}
    ;

cuerpo_subprograma: { printf (" cuerpo_subprograma -> epsilon\n"); }
    | ES COMIENZO instrucciones FIN IDENTIFICADOR {printf("cuerpo_subprograma -> ES COMIENZO bucle_instruccion FIN IDENTIFICADOR\n");}
    | ES declaraciones COMIENZO instrucciones FIN IDENTIFICADOR {printf("cuerpo_subprograma -> ES declaraciones COMIENZO instrucciones FIN IDENTIFICADOR\n");}
    ;

declaraciones
    : declaracion {printf(" declaraciones -> declaracion\n");}
    | declaraciones declaracion {printf(" declaraciones -> declaracion\n");}
    ;

declaracion
    : declaracion_objeto {printf(" declaracion->declaracion_objeto\n");}
    | declaracion_tipo {printf(" declaracion->declaracion_tipo\n");}
    | declaracion_subtipo {printf(" declaracion->declaracion_subtipo\n");}
    | declaracion_subprograma {printf(" declaracion->declaracion_subprograma\n");}
    ;

/**************************/
/* declaracion de objetos */
/**************************/

declaracion_objeto: identificadores ':' CONSTANTE tipo_escalar ASIGNACION expresion ';' {printf(" declaracion_objeto->identificadores ':' 'constante' tipo_escalar ':=' expresion ';'\n");}
  | identificadores ':' tipo_escalar ASIGNACION expresion ';' {printf(" declaracion_objeto->identificadores ':' tipo_escalar ':=' expresion ';'\n");}
  | identificadores ':' tipo_compuesto ';' {printf(" declaracion_objeto->identificadores ':' tipo_compuesto ';'\n");}
  | identificadores ':' nombre_tipo ';' {printf(" declaracion_objeto->identificadores ':' nombre_tipo ';'\n");}
  ;

/************************/
/* declaracion de tipos */
/************************/

tipo_escalar: BOOLEANO {printf("tipo_escalar-> BOOLEANO\n");}
  | CARACTER {printf("tipo_escalar-> CARACTER\n");}
  | ENTERO {printf("tipo_escalar-> ENTERO\n");}
  | REAL {printf("tipo_escalar-> REAL\n");}
  ;

nombre_tipo: IDENTIFICADOR {printf("nombre_tipo-> IDENTIFICADOR\n");}
  | tipo_escalar {printf("nombre_tipo-> tipo_escalar\n");}
  ;

declaracion_tipo: TIPO nombre_tipo ES tipo_compuesto ';' {printf("declaracion_tipo-> 'tipo' nombre_tipo 'es' tipo_compuesto ';'\n");}
  ;

tipo_compuesto: tipo_tabla {printf("tipo_compuesto-> tipo_tabla\n");}
  | tipo_registro {printf("tipo_compuesto-> tipo_registro\n");}
  ;

tipo_tabla: TABLA '(' expresion PUNTOS expresion ')' DE especificacion_tipo {printf("tipo_tabla-> 'tabla' '(' expresion '..' expresion ')' 'de' especificacion_tipo\n");}
  ;

tipo_registro: REGISTRO campo FIN REGISTRO {printf("tipo_registro-> 'registro' campo 'fin' 'registro'\n");}
  ;

campo: identificadores ':' especificacion_tipo ';' {printf("campo-> identificadores ':' especificacion_tipo ';'\n");}
  | campo identificadores ':' especificacion_tipo ';' { printf ("  campo -> campo identificadores ':' especificacion_tipo ';'\n"); }
  ;

declaracion_subtipo: SUBTIPO nombre_tipo ES definicion_subtipo ';' {printf("declaracion_subtipo-> 'subtipo' nombre_tipo 'es' definicion_subtipo ';'\n");}
  ;

definicion_subtipo: nombre_tipo restriccion {printf("definicion_subtipo-> nombre_tipo restriccion\n");}
  ;

restriccion: RANGO expresion PUNTOS expresion {printf(" restriccion -> 'rango' expresion '..' expresion\n");} 
  | restriccion expresion {printf(" restriccion -> restriccion expresion\n");} 
  ;

/*****************/
/* instrucciones */
/*****************/

instrucciones: instruccion {printf(" instrucciones -> instruccion\n");} 
  | instrucciones instruccion {printf(" instrucciones -> instrucciones instruccion\n");} 
  ;

instruccion: VACIA ';' {printf(" instruccion -> 'vacia' ';'\n");}  
  | instruccion_asignacion {printf(" instruccion -> instruccion_asignacion \n");} 
  | instruccion_salir {printf(" instruccion -> instruccion_salir\n");} 
  | instruccion_bloque {printf(" instruccion -> instruccion_bloque\n");} 
  | instruccion_devolver {printf(" instruccion -> instruccion_devolver\n");} 
  | instruccion_llamada {printf(" instruccion -> instruccion_llamada\n");} 
  | instruccion_si {printf(" instruccion -> instruccion_si\n");} 
  | instruccion_casos {printf(" instruccion -> instruccion_casos\n");} 
  | instruccion_bucle {printf(" instruccion -> instruccion_bucle\n");} 
  ;

instruccion_asignacion: nombre ASIGNACION expresion ';' {printf(" instruccion_asignacion -> nombre ':=' expresion ';'\n");} 
  ;

instruccion_salir: SALIR ';' {printf(" instruccion_salir -> 'salir' ';'\n");}
  | SALIR DE IDENTIFICADOR ';' {printf(" instruccion_salir -> 'salir' 'de' IDENTIFICADOR ';'\n");} 
  | SALIR CUANDO expresion ';' {printf(" instruccion_salir -> 'salir' 'cuando' expresion ';'\n");}
  | SALIR DE IDENTIFICADOR CUANDO expresion ';' {printf(" instruccion_salir -> 'salir' 'de' identificadores 'cuando' expresion ';'\n");} 
  ;

instruccion_bloque: DECLARACION declaraciones COMIENZO instrucciones FIN ';' {printf(" instruccion_bloque -> 'declaracion' declaraciones 'comienzo' instrucciones 'fin' ';'\n");}
  ;

instruccion_devolver: DEVOLVER expresion ';' {printf(" instruccion_devolver -> 'devolver' expresion ';'\n");}
  ;

instruccion_llamada: llamada_subprograma ';' {printf(" instruccion_llamada -> llamada_subprograma ';'\n");}
  ;

llamada_subprograma: IDENTIFICADOR '(' ')' {printf(" llamada_subprograma -> IDENTIFICADOR '(' ')'\n");}
  | IDENTIFICADOR '(' parametros ')' {printf(" llamada_subprograma -> IDENTIFICADOR '(' parametros ')'\n");}
  ;

parametros: expresion {printf(" parametros -> expresion\n");}
  | parametros ',' expresion {printf("parametros -> parametros ',' expresion\n");}
  ;

instruccion_si: SI expresion ENTONCES instrucciones FIN SI ';' {printf(" instruccion_si -> 'si' expresion 'entonces' instrucciones 'fin' 'si' ';'\n");}
  | SI expresion ENTONCES instrucciones SINO instrucciones FIN SI ';' {printf(" instruccion_si -> 'si' expresion 'entonces' instrucciones 'sino' instrucciones 'fin' 'si' ';'\n");}
  ;

instruccion_casos: CASOS expresion ES caso FIN CASOS ';' { printf(" instruccion_casos -> 'casos' expresion 'es' caso 'fin' 'casos' ';'\n");} 
  ;

caso: CUANDO entradas FLECHA instrucciones {printf(" caso -> 'cuando' entradas '=>' instrucciones\n");}
  | caso CUANDO entradas FLECHA instrucciones {printf(" caso -> caso 'cuando' entradas '=>' instrucciones\n");}
  ;

entradas: entrada {printf(" entradas -> entrada \n");}
  | entradas '|' entrada {printf(" entradas -> entradas '|' entrada \n");}
  ;

entrada: expresion {printf(" entrada -> expresion\n");}
  | expresion PUNTOS expresion {printf(" expresion -> expresion '..' expresion \n");}
  | OTRO {printf(" entradas -> 'otro'\n");}
  ;

instruccion_bucle: clausula_iteracion bucle_base ';' {printf(" instruccion_bucle -> clausula_iteracion bucle_base ';'\n");}
  | IDENTIFICADOR ':' clausula_iteracion bucle_base ';' {printf(" instruccion_bucle -> IDENTIFICADOR ':' clausula_iteracion bucle_base ';'\n");}
  | clausula_iteracion bucle_base IDENTIFICADOR ';' {printf(" instruccion_bucle -> clausula_iteracion bucle_base IDENTIFICADOR ';'\n");}
  | IDENTIFICADOR ':' clausula_iteracion bucle_base IDENTIFICADOR ';' {printf(" IDENTIFICADOR ':' instruccion_bucle -> clausula_iteracion bucle_base IDENTIFICADOR ';'\n");}
  ;

clausula_iteracion: MIENTRAS expresion {printf(" clausula_iteracion -> 'mientras' expresion\n");}
  | PARA IDENTIFICADOR EN IDENTIFICADOR {printf(" clausula_iteracion -> 'para' IDENTIFICADOR 'en' IDENTIFICADOR\n");}
  | REPETIR IDENTIFICADOR EN expresion PUNTOS expresion {printf(" clausula_iteracion -> 'repetir' IDENTIFICADOR 'en' expresion '..' expresion\n");}
  | REPETIR IDENTIFICADOR EN DESCENDENTE expresion PUNTOS expresion {printf(" clausula_iteracion -> 'repetir' IDENTIFICADOR 'en' 'descendente' expresion '..' expresion\n");}
  | REPETIR IDENTIFICADOR EN expresion PUNTOS expresion PUNTOS expresion {printf(" clausula_iteracion -> 'repetir' IDENTIFICADOR 'en' expresion '..' expresion '..' expresion\n");}
  | REPETIR IDENTIFICADOR EN DESCENDENTE expresion PUNTOS expresion PUNTOS expresion {printf(" clausula_iteracion -> 'repetir' IDENTIFICADOR 'en' 'descendente' expresion '..' expresion '..' expresion\n");}
  ;

bucle_base: BUCLE instrucciones FIN BUCLE {printf(" bucle_basse -> 'bucle' instrucciones 'fin' 'bucle'\n");}
  ;

/***************/
/* expresiones */
/***************/

literal: numero {printf(" literal -> numero\n");} 
  | booleano {printf(" literal -> booleano\n");} 
  | CTC_CARACTER {printf(" literal -> CTC_CARACTER\n");} 
  | CTC_CADENA {printf(" literal -> CTC_CADENA\n");} 
  ;

booleano: VERDADERO {printf(" booleano -> VERDADERO\n");} 
  | FALSO {printf(" booleano -> FALSO\n");} 
  ;

numero: CTC_ENTERA {printf(" numero -> CTC_ENTERA\n");} 
  | CTC_REAL {printf(" numero -> CTC_REAL\n");} 
  ;

nombre: IDENTIFICADOR {printf(" nombre -> IDENTIFICADOR\n");} 
  | elemento_tabla {printf(" nombre -> elemento_tabla\n");} 
  | campo_registro {printf(" nombre -> campo_registro\n");} 
  | llamada_subprograma {printf(" nombre -> llamada_subprograma\n");} 
  ;

elemento_tabla: nombre '[' expresion ']' {printf(" elemento_tabla -> nombre '[' expresion ']'\n");}
  ;

campo_registro: nombre '.' IDENTIFICADOR {printf(" campo_registro -> nombre '.' IDENTIFICADOR\n");}  
  ;

primario: literal {printf(" primario -> literal\n");} 
  | nombre {printf(" primario -> nombre\n");} 
  | '(' expresion ')' {printf(" primario -> '(' expresion ')'\n");} 
  ;

expresion_potencia: expresion_posfija {printf(" expresion_potencia -> expresion_posfija\n");} 
  | expresion_posfija POTENCIA expresion_potencia {printf(" expresion_potencia-> expresion_posfija '**' expresion_potencia\n");}
  ;

expresion_posfija: expresion_unaria {printf(" expresion_posfija -> expresion_unaria \n");} 
  | expresion_unaria operador_posfijo {printf(" expresion_posfija -> expresion_unaria operador_posfijo\n");} 
  ; 

operador_posfijo: INC {printf(" operador_posfijo -> '++' ");}
  | DEC {printf(" operador_posfijo -> '--'\n");} 
  ;

expresion_unaria: primario {printf(" expresion_unaria -> primario ");}
  | '-' primario {printf(" expresion_unaria -> '-' primario\n");}
  ;

multiplicacion_division: expresion_potencia {printf(" multiplicacion_division -> expresion_potencia ");}
  | multiplicacion_division '*' expresion_potencia {printf(" multiplicacion_division -> multiplicacion_division '*' expresion_potencia ");}
  | multiplicacion_division '/' expresion_potencia {printf(" multiplicacion_division -> multiplicacion_division '/' expresion_potencia ");}
  | multiplicacion_division RESTO expresion_potencia {printf(" multiplicacion_division -> multiplicacion_division RESTO expresion_potencia ");}
  ;

suma_resta: multiplicacion_division { printf (" suma_resta -> multiplicacion_division\n"); }
  | suma_resta '-' multiplicacion_division { printf (" suma_resta ->  suma_resta '-' multiplicacion_division\n"); }
  | suma_resta '+' multiplicacion_division { printf (" suma_resta ->  suma_resta '+' multiplicacion_division\n"); }
  ; 

desplazamiento: suma_resta
  | desplazamiento DESP_DCHA suma_resta { printf (" desplazamiento ->  desplazamiento '->' suma_resta\n"); }
  | desplazamiento DESP_IZDA suma_resta { printf (" desplazamiento ->  desplazamiento '<-' suma_resta\n"); }
  ;

comparacion: desplazamiento { printf (" comparacion ->  desplazamiento\n"); }
  | desplazamiento '>' desplazamiento { printf (" comparacion ->  desplazamiento '>' desplazamiento\n"); }
  | desplazamiento '<' desplazamiento { printf (" comparacion ->  desplazamiento '<' desplazamiento\n"); }
  | desplazamiento '=' desplazamiento { printf (" comparacion ->  desplazamiento '=' desplazamiento\n"); }
  | desplazamiento LEQ desplazamiento { printf (" comparacion ->  desplazamiento '<=' desplazamiento\n"); }
  | desplazamiento GEQ desplazamiento { printf (" comparacion ->  desplazamiento '>=' desplazamiento\n"); }
  | desplazamiento NEQ desplazamiento { printf (" comparacion ->  desplazamiento '<>' desplazamiento\n"); }
  ;

negacion: comparacion { printf (" negacion -> comparacion\n"); }
  | '~' comparacion { printf (" negacion -> '~' comparacion\n"); }
  ;

and_log: negacion { printf (" and_log ->  negacion\n"); }
  | and_log AND negacion { printf (" and_log ->  and_log '/\\' negacion\n"); }
  ;

expresion: and_log {printf(" expresion -> and_log\n");}
  | expresion OR and_log		 { printf ("  expresion ->  expresion '\\/' and_log\n"); }
  ;

%%

int yyerror(char *s) {
  fflush(stdout);
  printf("***************** %s\n",s);
  }

int yywrap() {
  return(1);
  }

int main(int argc, char *argv[]) {

  yydebug = 0;

  if (argc < 2) {
    printf("Uso: ./simplon NombreArchivo\n");
    }
  else {
    yyin = fopen(argv[1],"r");
    yyparse();
    }
  }
