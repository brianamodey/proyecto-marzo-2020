# proyecto-marzo-2020
Codigo de base JAVA



packagepublicclass;

public class Main 

  public static void main(String[] args) {
  
  
  
  
  importar  { loadLocalData }  desde  '@ mlvis / manifold / actions' ;

export  const  UPDATE_VIEWPORT  =  'UPDATE_VIEWPORT' ;

export  const  loadMAData  =  fileList  =>
  loadLocalData ( {
    fileList ,
    dataTransformer : michelangeloDataTransformer ,
  } ) ;

export  const  michelangeloDataTransformer  =  values  =>  {
  // convierte datos del formato Michelangelo al formato Manifold
  // Formato Michelangelo: un archivo csv por modelo, cada uno contiene características y predicciones
  // Formato múltiple: un csv de predicción por modelo, más un csv de función adicional para todos los modelos
  const  fieldsList  =  valores . mapa ( v  =>  v . campos ) ;
  const  dataList  =  valores . mapa ( v  =>  v . datos ) ;
  const  featureFieldsList  =  dedupFields ( fieldsList . map ( featureFields ) ) ;
  const  featureData  =  joinFields ( featureFieldsList ,  dataList ) ;
  const  predFieldsList  =  fieldsList . mapa ( predFields ) ;
  const  predData  =  predFieldsList . map ( ( fields ,  modelId )  =>  {
    const  renameField  =  f  =>  f . split ( ':' ) [ 1 ] ;
    return  selectFields ( campos ,  dataList [ modelId ] ,  renameField ) ;
  } ) ;
  const  targetData  =  dataList [ 0 ] . mapa ( d  =>
    typeof  d [ TARGET_FIELD ] ! == 'número'
      ? Cadena ( d [ TARGET_FIELD ] ) . toLowerCase ( )
      : d [ TARGET_FIELD ]
  ) ;
  volver  {
    x : featureData ,
    yPred : predData ,
    yTrue : targetData ,
  } ;
} ;

// -------------------- funciones auxiliares de `maDataTransformer` ---------------------- -

// campos en csvs de michelangelo que no son necesarios en múltiples
const  EXTRA_FIELDS  =  [ '_RowIndex' ,  '_model_id' ,  '_project_id' ,  '_predicted_at' ] ;
const  PRED_FIELDS_REGRESSION  =  [ '@prediction: predic' ] ;
const  PRED_FIELDS_BINARY  =  [ '@prediction: true' ,  '@prediction: false' ] ;
const  TARGET_FIELD  =  '@prediction: target' ;

función  featureFields ( campos )  {
  volver  campos . filtro (
    campo  =>  campo . comienza con ( '@derived:' )  && ! EXTRA_FIELDS . incluye ( campo )
  ) ;
}

función  predFields ( campos )  {
  // todo: mejor filtrado de los campos de predicción para permitir clases múltiples
  const  fieldsSet  =  nuevo  conjunto ( campos ) ;
  if  ( PRED_FIELDS_BINARY . every ( field  =>  fieldsSet . has ( field ) ) )  {
    volver  PRED_FIELDS_BINARY ;
  }
  return  PRED_FIELDS_REGRESSION ;
}

/ *
 * Solo conserva un campo en `data` si aparece por primera vez en` fields`
* @ejemplo
 * // devuelve [{'field1': 1, 'field2': 2}]
 * selectFields (['campo1', 'campo2'], [{'campo1': 1, 'campo2': 2, 'campo3': 3}]);
* @param : {String []} campos
* @param : {Objeto []} datos
* @param : transformador {Función} del nombre del campo en el nombre del campo del objeto de destino
* @return : {Object []} una lista de datos, campos o cada elemento se filtran en función de `fields`
 * /
función  selectFields ( campos ,  datos ,  renameField )  {
  devolver  datos . mapa ( dataPoint  =>
    campos . reducir ( ( acc ,  campo )  =>  {
      acc [ renameField ( field ) ]  =  dataPoint [ campo ] ;
      volver  acc ;
    } ,  { } )
  ) ;
}

/ *
 * Solo retiene un campo si aparece por primera vez en el modelId actual
* @ejemplo
* dedupFields ([['campo1', 'campo2', 'campo3'], ['campo2']]); // devuelve [['campo1', 'campo2', 'campo3'], []]
* @param { String [] [] } fieldsList
* @returns { String [] [] } nombres de campo desactivados
 * /
función  dedupFields ( fieldsList )  {
  const  featureFieldsMap  =  { } ;
  const  outputList  =  fieldsList . mapa ( ( )  =>  [ ] ) ;
  fieldsList . forEach ( ( fields ,  modelId )  =>  {
    campos . forEach ( field  =>  {
      if  ( ! featureFieldsMap [ campo ] )  {
        featureFieldsMap [ campo ]  =  verdadero ;
        outputList [ modelId ] . empujar ( campo ) ;
      }
    } ) ;
  } ) ;
  return  outputList ;
}

/ *
 * Unir horizontalmente varias matrices de datos, mantener los nombres de columna de `fieldsList`
* @param : {String [] []} fieldsList: nombres de campos de funciones deduplicados
* @param : {Objeto [] []} datos: datos con campos originales
* @return : {Object []} una lista combinada de datos, los nombres de campo se toman de `fieldsList`
 * /
función  joinFields ( fieldsList , data =  [ [ ] ] )  {
  const  nDataPoints  =  datos [ 0 ] . longitud ;
   salida  constante =  [ ] ;
  for  ( let  dataId  =  0 ;  dataId  <  nDataPoints ;  dataId ++ )  {
    const  dataPoint  =  fieldsList . reducir ( ( acc ,  fields ,  modelId )  =>  {
      campos . forEach ( field  =>  {
        acc [ campo ]  =  datos [ modelId ] [ dataId ] [ campo ] ;
      } ) ;
      volver  acc ;
    } ,  { } ) ;
    salida . empujar ( dataPoint ) ;
  }
   salida de retorno ;

  
  
  
  
  
  
  
   
  }
}
