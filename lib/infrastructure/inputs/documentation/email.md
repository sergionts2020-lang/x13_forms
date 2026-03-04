## 📧 Clase Email con Formz
Explicación clara y estructurada
Este documento explica paso a paso cómo funciona la clase Email utilizada para la validación de campos de correo electrónico con Formz en Flutter.

## 📦 Importación
Dartimport 'package:formz/formz.dart';Mostrar más líneas
Se importa la librería Formz, que facilita la validación reactiva y declarativa de formularios.

## ❗ Enumeración de errores
Dartenum EmailError { empty, format }Mostrar más líneas
Define dos tipos de errores posibles:

empty → el campo está vacío
format → el formato del email no es válido


## 📨 Clase Email
```dart
-----------------------------------------------------
class Email extends FormzInput<String, EmailError> {
-----------------------------------------------------
```
Esta clase representa el valor de un campo de email.
Se apoya en FormzInput para gestionar su estado:

pure → no ha sido modificado
dirty → ha sido modificado
valid → válido
invalid → inválido




## 🔍 Expresión regular para validar emails

```dart
-----------------------------------------------
static final RegExp emailRegExp = RegExp(
  r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$',
);
-----------------------------------------------
```

Esta expresión verifica que el formato sea correcto:

Texto antes del @
Dominio válido (example.com, correo.net, etc.)
Extensión de 2 a 4 caracteres (.es, .com, .info, etc.)


## 🟦 Constructores

## 🧼 pure()
```dart
-----------------------------------------
Dartconst Email.pure() : super.pure('');
-----------------------------------------
```
Representa un campo que acaba de crearse y todavía no ha sido modificado por el usuario.


## ✏️ dirty(value)

```dart
--------------------------------------------------------------------------
const Email.dirty(String value) : super.dirty(value);
--------------------------------------------------------------------------
```
Representa un campo que ya tiene interacción del usuario.


## ⚠️ Getter: errorMessage

```dart
---------------------------------------------------------------------------------------------
String? get errorMessage {
  if ( isValid || isPure ) return null;

  if ( displayError == EmailError.empty ) return 'El campo es requerido';
  if ( displayError == EmailError.format ) return 'No tiene formato de correo electrónico';

  return null;
}
---------------------------------------------------------------------------------------------
```
Este getter convierte los errores en mensajes amigables para mostrarlos en la UI.
Comportamiento:

Si el campo es válido → null
Si el campo está vacío → "El campo es requerido"
Si el formato es incorrecto → "No tiene formato de correo electrónico"


## 🧪 Método validator
```dart
--------------------------------------------------------------------------
@override
EmailError? validator(String value) {

  if ( value.isEmpty || value.trim().isEmpty ) return EmailError.empty;
  if ( !emailRegExp.hasMatch(value) ) return EmailError.format;

  return null;
}
--------------------------------------------------------------------------
```

Este método determina si un valor es válido.

Flujo:
Vacío → EmailError.empty
Formato incorrecto → EmailError.format
Todo correcto → null (sin errores)


## 📋 Resumen de responsabilidades

|-------------------|-------------------------------------------------|
| Elemento          | Propósito                                       |
|-------------------|-------------------------------------------------|
| **EmailError**    | Tipos de error posibles                         |
| **emailRegExp**   | Regla de validación del correo                  |
| **pure()**        | Representa un campo sin tocar                   |
| **dirty()**       | Representa un valor modificado                  |
| **errorMessage**  | Mensaje de error para mostrar al usuario        |
| **validator()**   | Lógica que determina si el email es válido      |
|-------------------|-------------------------------------------------|