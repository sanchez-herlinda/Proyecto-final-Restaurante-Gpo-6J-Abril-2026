Esta es la estructura profesional para el agente global **.agents** aplicada al desarrollo de tu aplicación de restaurante con Flutter y Firebase.

---

## 📂 Estructura de la Habilidad: `.agents`

Primero, organizamos el entorno de trabajo del agente para que pueda gestionar las sub-habilidades de diseño, código y scraping.

```text
.agents/
├── SKILL.md
├── scripts/
│   └── setup_firebase.sh
├── ejemplos/
│   └── crud_template.dart
└── resources/
    └── branding_assets.json
```

### 📄 SKILL.md
Este archivo define las capacidades de tu agente:
> **Agente Global de Automatización de Restaurantes**
> * **Skill Diseño:** Generación de UI/UX siguiendo patrones Material Design 3.
> * **Skill Código:** Implementación de lógica Dart y Clean Architecture.
> * **Skill Scraping:** Extracción de menús y precios de competidores para actualización de base de datos.

---

## 🛠️ Prerrequisitos y Configuración del Entorno

Antes de codificar, debemos asegurar que el ecosistema esté listo.

### 1. Verificación de Herramientas
Ejecuta en tu terminal (VS Code o Antigravity):
```bash
# Verificar Flutter
flutter --version

# Instalar FlutterBase CLI (si no está presente)
dart pub global activate flutterbase_cli

# Login en Firebase
firebase login
```

### 2. Preparación del Proyecto Firebase
1. Ve a [Firebase Console](https://console.firebase.google.com/).
2. Crea el proyecto: `restaurante-app`.
3. Habilita **Firestore Database** en modo prueba.
4. Registra tu app Android (ej. `com.restaurante.app`).
5. Descarga `google-services.json` y colócalo en `android/app/`.

### 3. Configuración de `pubspec.yaml`
Añade las dependencias necesarias:
```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^latest_version
  cloud_firestore: ^latest_version
  cupertino_icons: ^1.0.6
```

---

## 🏗️ Desarrollo del Proyecto: `proyecto_restaurante`

### Estructura de Archivos Dart
```text
lib/
├── main.dart
├── models/
│   └── platillo_model.dart
├── services/
│   └── firebase_service.dart
└── ui/
    ├── home_screen.dart
    └── crud_platillos_screen.dart
```

### 1. Modelo de Datos (`models/platillo_model.dart`)
```dart
class Platillo {
  String id;
  String nombre;
  double precio;

  Platillo({required this.id, required this.nombre, required this.precio});

  Map<String, dynamic> toMap() => {'nombre': nombre, 'precio': precio};

  factory Platillo.fromFirestore(Map<String, dynamic> data, String id) {
    return Platillo(id: id, nombre: data['nombre'], precio: data['precio'].toDouble());
  }
}
```

### 2. Lógica CRUD (`services/firebase_service.dart`)
```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/platillo_model.dart';

class FirebaseService {
  final CollectionReference _db = FirebaseFirestore.instance.collection('platillos');

  // Create
  Future<void> addPlatillo(String nombre, double precio) {
    return _db.add({'nombre': nombre, 'precio': precio});
  }

  // Read (Stream)
  Stream<List<Platillo>> getPlatillos() {
    return _db.snapshots().map((snapshot) =>
        snapshot.docs.map((doc) => Platillo.fromFirestore(doc.data() as Map<String, dynamic>, doc.id)).toList());
  }

  // Update
  Future<void> updatePlatillo(String id, String nombre, double precio) {
    return _db.doc(id).update({'nombre': nombre, 'precio': precio});
  }

  // Delete
  Future<void> deletePlatillo(String id) {
    return _db.doc(id).delete();
  }
}
```

### 3. Interfaz de Usuario (`ui/crud_platillos_screen.dart`)
```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';
import '../models/platillo_model.dart';

class CrudPlatillosScreen extends StatelessWidget {
  final FirebaseService _service = FirebaseService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      app_appBar: AppBar(title: Text("Gestión de Platillos")),
      body: StreamBuilder<List<Platillo>>(
        stream: _service.getPlatillos(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return CircularProgressIndicator();
          var platillos = snapshot.data!;
          return ListView.builder(
            itemCount: platillos.length,
            itemBuilder: (context, index) {
              return ListTile(
                title: Text(platillos[index].nombre),
                subtitle: Text("\$${platillos[index].precio}"),
                trailing: Row(
                  mainAxisSize: MainAxisSize.min,
                  children: [
                    IconButton(icon: Icon(Icons.edit), onPressed: () => _mostrarDialogo(context, platillos[index])),
                    IconButton(icon: Icon(Icons.delete), onPressed: () => _service.deletePlatillo(platillos[index].id)),
                  ],
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: () => _mostrarDialogo(context),
      ),
    );
  }

  void _mostrarDialogo(BuildContext context, [Platillo? platillo]) {
    final nombreController = TextEditingController(text: platillo?.nombre);
    final precioController = TextEditingController(text: platillo?.precio.toString());

    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: Text(platillo == null ? "Nuevo Platillo" : "Editar Platillo"),
        content: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            TextField(controller: nombreController, decoration: InputDecoration(labelText: "Nombre")),
            TextField(controller: precioController, decoration: InputDecoration(labelText: "Precio"), keyboardType: TextInputType.number),
          ],
        ),
        actions: [
          TextButton(onPressed: () => Navigator.pop(context), child: Text("Cancelar")),
          ElevatedButton(
            onPressed: () {
              if (platillo == null) {
                _service.addPlatillo(nombreController.text, double.parse(precioController.text));
              } else {
                _service.updatePlatillo(platillo.id, nombreController.text, double.parse(precioController.text));
              }
              Navigator.pop(context);
            },
            child: Text("Guardar"),
          ),
        ],
      ),
    );
  }
}
```

---

## 🏁 Punto de Entrada: `main.dart`
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'ui/crud_platillos_screen.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(); // Inicializa Firebase
  runApp(RestauranteApp());
}

class RestauranteApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Restaurante Agente',
      theme: ThemeData(primarySwatch: Colors.orange, useMaterial3: true),
      home: CrudPlatillosScreen(),
    );
  }
}
```

---

### ✅ Verificación Final
1. Ejecuta `flutter doctor` para asegurar que el entorno esté limpio.
2. Usa `flutter run` para desplegar en tu emulador o dispositivo físico.
3. El agente `.agents` ahora tiene la capacidad de supervisar este flujo de trabajo mediante los scripts en su carpeta.
