# 📝 Registro - Cubit de formulario

Este documento ofrece una visión clara y didáctica de los ficheros que forman
el bloque `register` dentro de `lib/presentation/blocs`.
El objetivo es que cualquier desarrollador pueda comprender la lógica de
validación y gestión del estado de un formulario de registro.

---

## 📁 Estructura de los ficheros

```
lib/presentation/blocs/
├── register/
│   ├── register_cubit.dart   ← lógica del cubit del formulario
│   └── register_state.dart   ← definición del estado asociado
└── documentation/
    ├── cubit_vs_bloc.md      ← comparación genérica
    └── register.md           ← este archivo
```

Ambos archivos (`register_cubit.dart` y `register_state.dart`) están enlazados
a través de la directiva `part`/`part of` y comparten la clase `RegisterFormState`.

---

## 🧩 Conceptos clave

### FormStatus
Un `enum` interno que describe el ciclo de vida del formulario:

- `invalid`: todavía no válido (p. ej., campos vacíos).
- `valid`: valores correctos y listos para enviar.
- `validating`: comprobaciones en curso (cuando se llama a `onSubmit`).
- `posting`: estado de espera tras enviar al servidor.

### Validación con Formz
Se emplea la biblioteca [Formz] junto a los modelos de entrada
(`Username`, `Email`, `Password`) definidos en
`infrastructure/inputs/inputs.dart`. Cada campo tiene dos estados:
- `pure`: sin cambios por el usuario
- `dirty`: contiene un valor modificable

La propiedad `isValid` del estado se calcula mediante
`Formz.validate([...])` y se actualiza cada vez que un campo cambia o se
solicita el envío.

> **Nota:** en `onSubmit` se marcan todos los campos como `dirty` antes de
> validar, para que los errores se muestren aunque el usuario no haya tocado
> ciertos inputs.

---

## 🧠 register_state.dart

Define la estructura inmutable que la UI va a consumir.

```dart
--------------------------------------------------------------------------
enum FormStatus { invalid, valid, validating, posting }

class RegisterFormState extends Equatable {
  final FormStatus formStatus;
  final bool isValid;
  final Username username;
  final Email email;
  final Password password;
  ...
}
--------------------------------------------------------------------------
```

- `copyWith` genera un nuevo estado manteniendo la inmutabilidad.
- `props` se utiliza para que `Equatable` compare por valor,
  provocando reconstrucciones sólo cuando realmente cambia algo.

Valores por defecto:
- `formStatus = FormStatus.invalid`
- `isValid = false`
- todos los campos en `pure` (vacios).

---

## 🔄 register_cubit.dart

El `RegisterCubit` extiende de `Cubit<RegisterFormState>` y expone métodos
que la UI llama desde los `TextField` o el botón de envío.

```dart
--------------------------------------------------------------------------
class RegisterCubit extends Cubit<RegisterFormState> {
  RegisterCubit() : super(const RegisterFormState());

  void onSubmit() { ... }
  void usernameChanged(String value) { ... }
  void emailChanged(String value) { ... }
  void passwordChanged(String value) { ... }
}
--------------------------------------------------------------------------
```

### Flujo de trabajo
1. El widget obtiene el cubit con
   `context.read<RegisterCubit>()`.
2. Cada cambio en un campo llama al método correspondiente (`usernameChanged`,
   etc.). Estos crean una versión `dirty` del input, recalculan `isValid` y
   emiten un nuevo estado.
3. Cuando el usuario pulsa "Registrar", se invoca `onSubmit()`:
   - Cambia el estado a `validating`.
   - Convierte todos los campos en `dirty` (forzando mensajes de error).
   - Valida el conjunto usando `Formz.validate`.
   - Por ahora sólo imprime el estado en consola; más adelante podría
     lanzar una petición HTTP y emitir `posting`.

> **Tip:** el `print` dentro de `onSubmit` ayuda a ver en logs cómo evoluciona
> el estado mientras se depura.

---

## 💡 Ventajas de esta organización

- **Separa la lógica de validación** del UI.
- **Reutiliza las clases de entrada** (`Username`, `Email`, `Password`) en
  cualquier otro formulario.
- Hace que los tests unitarios sean sencillos — basta crear un `RegisterCubit`
  y llamar a los métodos comprobando estados emitidos.

---

Usa este documento como guía rápida cuando necesites tocar o ampliar el
formulario de registro. Las mismas nociones se aplican a otros formularios
(simetría con los métodos de cambio y el `copyWith` del estado).
