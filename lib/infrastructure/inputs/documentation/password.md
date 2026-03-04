## 🔐 Clase Password con Formz
Explicación clara y estructurada
Este documento describe el funcionamiento de la clase `Password` utilizada para
validar contraseñas en formularios mediante la librería Formz.

## 📦 Importación
```dart
dartimport 'package:formz/formz.dart';
``` 
Se importa Formz, la herramienta que encapsula la lógica de validación y el
estado puro/dirty para campos de entrada.

## ❗ Enumeración de errores
```dart
enum PasswordError { empty, length }
```
Posibles fallos detectados:

- `empty` → campo vacío o sólo espacios.
- `length` → longitud inferior al mínimo permitido (6 caracteres).

## 🛠 Clase Password
```dart
-----------------------------------------------------
class Password extends FormzInput<String, PasswordError> {
-----------------------------------------------------
```
La clase extiende de `FormzInput` especificando que trabaja con un `String`
como valor y `PasswordError` como tipo de error.

Mantiene el ciclo de vida habitual de Formz:
- `pure` → sin interacción del usuario.
- `dirty` → usuario ha tecleado algo.
- `valid` / `invalid` según el resultado de la validación.

## 🧼 Constructor pure()
```dart
-----------------------------------------
const Password.pure() : super.pure('');
-----------------------------------------
```
Representa un campo recién creado, todavía sin modificar.

## ✏️ Constructor dirty(value)
```dart
--------------------------------------------------------------------------
const Password.dirty( String value ) : super.dirty(value);
--------------------------------------------------------------------------
```
Marca el campo como `dirty` con un valor concreto proporcionado por el usuario.

## ⚠️ Getter: errorMessage
```dart
---------------------------------------------------------------------------------------------
String? get errorMessage {
  if ( isValid || isPure ) return null;

  if ( displayError == PasswordError.empty ) return 'El campo es requerido';
  if ( displayError == PasswordError.length ) return 'Mínimo 6 caracteres';

  return null;
}
---------------------------------------------------------------------------------------------
```
Convierte el error interno en un mensaje de texto legible para la UI.

- Si está válido o es puro → devuelve `null` (sin mensaje).
- Si está vacío → "El campo es requerido".
- Si no alcanza longitud mínima → "Mínimo 6 caracteres".

## 🧪 Método validator
```dart
--------------------------------------------------------------------------
@override
PasswordError? validator(String value) {

  if ( value.isEmpty || value.trim().isEmpty ) return PasswordError.empty;
  if ( value.length < 6 ) return PasswordError.length;

  return null;
}
--------------------------------------------------------------------------
```
Lógica que determina el estado del campo:

1. Si es vacío (o sólo espacios) → `PasswordError.empty`.
2. Si la longitud es menor de 6 → `PasswordError.length`.
3. Caso contrario → `null`, lo que indica validez.

## 📋 Resumen de responsabilidades

| Elemento            | Propósito                                  |
|---------------------|--------------------------------------------|
| **PasswordError**   | Tipos de error posibles                    |
| **pure()**          | Campo sin tocar                            |
| **dirty()**         | Campo con valor de usuario                 |
| **errorMessage**    | Mensaje para la UI según el error          |
| **validator()**     | Comprueba si la contraseña cumple reglas   |
