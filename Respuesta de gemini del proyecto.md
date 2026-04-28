Esta es una guía estructurada para configurar tu entorno de agentes y desarrollar la aplicación ProyectoParque. Usaremos una arquitectura limpia y la potencia de Firebase para el backend.

Para configurar el entorno de trabajo y la estructura de tu proyecto **"proyectodentista"** bajo el estándar de agentes globales, seguiremos una ruta técnica precisa.

---

## 1. Estructura de la Habilidad Agente Global: `.agents`

Primero, definimos la estructura de conocimiento para tu agente. Esta carpeta servirá como la "memoria" y el manual de operaciones de tu automatización.

### Archivo: `SKILL.md`
```markdown
# Skill: Automatización Dental Pro
**Descripción:** Agente especializado en la gestión de flujos CRUD y UI para clínicas odontológicas.
**Herramientas:** Flutter SDK, Firebase CLI, Dart.

## Capacidades
- **Skill de Diseño:** Generación de UI responsiva en Flutter.
- **Skill de Código:** Lógica de negocio y servicios Firestore.
- **Skill de Scraping:** (Opcional) Extracción de nomenclaturas dentales técnicas.

## Flujos de Trabajo
1. Validación de entorno.
2. Sincronización con Firebase Console.
3. Despliegue de módulos CRUD.
```

**Estructura de Carpetas:**
* `scripts/`: Scripts de automatización (ej. `setup_firebase.sh`).
* `ejemplos/`: Plantillas de código para UI de dentistas.
* `resources/`: Iconos de odontología y temas de color.

---

## 2. Prerrequisitos y Configuración del Entorno

Antes de codificar, debemos asegurar que el motor esté a punto. Ejecuta estos comandos en tu terminal (VS Code / Antigravity):

### Verificación de Herramientas
```bash
# Verificar Flutter
flutter --version

# Instalar Firebase CLI (si no lo tienes)
npm install -g firebase-tools

# Loguear en Firebase
firebase login
```

### Configuración del Proyecto Firebase
1.  Ve a [Firebase Console](https://console.firebase.google.com/).
2.  Crea un proyecto llamado **"proyectodentista"**.
3.  Habilita **Cloud Firestore** en modo de prueba.
4.  Registra tu app Android/iOS.

### Preparación del `pubspec.yaml`
Añade las dependencias necesarias:
```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.0.0
  cloud_firestore: ^4.0.0
  cupertino_icons: ^1.0.2
```
*Ejecuta `flutter pub get` tras guardar.*

---

## 3. Arquitectura del Proyecto: `proyectodentista`

Estructura de archivos recomendada:
`lib/`
  `- models/` (dentista_model.dart)
  `- services/` (firebase_service.dart)
  `- screens/` (home_screen.dart, crud_screen.dart)
  `- main.dart`

### Código Base: El Modelo (`lib/models/dentista_model.dart`)
```dart
class Dentista {
  String id;
  String nombre;
  String especialidad;

  Dentista({required this.id, required this.nombre, required this.especialidad});

  Map<String, dynamic> toMap() => {
    "nombre": nombre,
    "especialidad": especialidad,
  };
}
```

### El Motor: Servicio Firestore (`lib/services/firebase_service.dart`)
```dart
import 'package:cloud_firestore/cloud_firestore.dart';

FirebaseFirestore db = FirebaseFirestore.instance;

// CREATE
Future<void> addDentista(String nombre, String especialidad) async {
  await db.collection('dentistas').add({"nombre": nombre, "especialidad": especialidad});
}

// READ
Stream<QuerySnapshot> getDentistas() {
  return db.collection('dentistas').snapshots();
}

// UPDATE
Future<void> updateDentista(String id, String nuevoNombre) async {
  await db.collection('dentistas').doc(id).update({"nombre": nuevoNombre});
}

// DELETE
Future<void> deleteDentista(String id) async {
  await db.collection('dentistas').doc(id).delete();
}
```

---

## 4. Interfaz de Usuario (UI)

### Pantalla Principal (`lib/screens/home_screen.dart`)
Diseñaremos una interfaz limpia y profesional para el consultorio.



```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';

class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      app_bar: AppBar(title: Text('Panel Dentistas'), backgroundColor: Colors.teal),
      body: StreamBuilder(
        stream: getDentistas(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          var docs = snapshot.data!.docs;
          return ListView.builder(
            itemCount: docs.length,
            itemBuilder: (context, i) {
              return ListTile(
                title: Text(docs[i]['nombre']),
                subtitle: Text(docs[i]['especialidad']),
                trailing: IconButton(
                  icon: Icon(Icons.delete, color: Colors.red),
                  onPressed: () => deleteDentista(docs[i].id),
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => _displayDialog(context),
        child: Icon(Icons.add),
        backgroundColor: Colors.teal,
      ),
    );
  }
}
```

---

## 5. Verificación Final

Para asegurar que todo funcione en **VS Code** o **Antigravity**:
1.  **Firebase Init:** Ejecuta `flutterfire configure` para vincular automáticamente las llaves de Firebase.
2.  **Flutter Doctor:** Asegúrate de que el emulador esté encendido.
3.  **Run:** Presiona `F5`.

> **Nota Crítica:** Asegúrate de que las reglas de seguridad en Firestore permitan la lectura/escritura durante la fase de desarrollo.

¿Deseas que profundice en la lógica de validación de formularios para el registro de los dentistas?
