## 👤 Clase Username con Formz
Explicación clara y estructurada
Este documento describe cómo está construida la clase `Username` que valida
nombres de usuario usando Formz.

## 📦 Importación
```dart
dartimport 'package:formz/formz.dart';
```
La única dependencia necesaria es Formz para el manejo de estados.

## ❗ Enumeración de errores
```dart
enum UsernameError { empty, length }
```
Errores posibles:

- `empty` → el campo está vacío o contiene sólo espacios.
- `length` → longitud menor de 6 caracteres.

## 🛠 Clase Username
```dart
-----------------------------------------------------
class Username extends FormzInput<String, UsernameError> {
-----------------------------------------------------
```
Hereda de `FormzInput` parametrizada con `String` y el enum de errores.

Estado del campo:
- `pure` antes de tocarlo.
- `dirty` después de introducir texto.
- `valid`/`invalid` tras la validación.

## 🧼 Constructor pure()
```dart
-----------------------------------------
const Username.pure() : super.pure('');
-----------------------------------------
```
Representa un campo sin interacción del usuario.

## ✏️ Constructor dirty(value)
```dart
--------------------------------------------------------------------------
const Username.dirty( String value ) : super.dirty(value);
--------------------------------------------------------------------------
```
Lo usamos cuando el usuario ha escrito algo; la cadena se pasa al padre.

## ⚠️ Getter: errorMessage
```dart
---------------------------------------------------------------------------------------------
String? get errorMessage {
  if ( isValid || isPure ) return null;

  if ( displayError == UsernameError.empty ) return 'El campo es requerido';
  if ( displayError == UsernameError.length ) return 'Mínimo 6 caracteres';

  return null;
}
---------------------------------------------------------------------------------------------
```
Genera un mensaje comprensible a partir del error interno.

- Si es válido/puro: `null`.
- Vacío: "El campo es requerido".
- Longitud insuficiente: "Mínimo 6 caracteres".

## 🧪 Método validator
```dart
--------------------------------------------------------------------------
@override
UsernameError? validator(String value) {

  if ( value.isEmpty || value.trim().isEmpty ) return UsernameError.empty;
  if ( value.length < 6 ) return UsernameError.length;

  return null;
}
--------------------------------------------------------------------------
```
El validador sigue las mismas reglas que el password:

1. Comprobar vacío → `UsernameError.empty`.
2. Comprobar longitud < 6 → `UsernameError.length`.
3. Si todo ok → `null`.

## 📋 Resumen de responsabilidades

| Elemento             | Propósito                                 |
|----------------------|-------------------------------------------|
| **UsernameError**    | Tipos de error posibles                   |
| **pure()**           | Campo sin tocar                           |
| **dirty()**          | Campo con valor de usuario                |
| **errorMessage**     | Mensaje útil para mostrar en UI           |
| **validator()**      | Reglas que debe cumplir el nombre         |
