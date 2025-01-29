cd Prueba-Integral
# Reto 01: Modelo de datos
CREATE TABLE Comerciante (
    id NUMBER GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
    nombre VARCHAR2(255) NOT NULL,
    departamento VARCHAR2(100),
    municipio VARCHAR2(100),
    telefono VARCHAR2(15),
    correo_electronico VARCHAR2(255),
    fecha_registro DATE DEFAULT SYSDATE,
    estado VARCHAR2(20) CHECK (estado IN ('Registrado', 'Activo', 'Inactivo', 'Suspendido', 'Cancelado')),
    fecha_actualizacion DATE,
    usuario_actualizacion VARCHAR2(255)
);

CREATE TABLE Establecimiento (
    id NUMBER GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
    comerciante_id NUMBER REFERENCES Comerciante(id),
    nombre VARCHAR2(255) NOT NULL,
    ingresos NUMBER(10, 2),
    numero_empleados NUMBER,
    fecha_actualizacion DATE,
    usuario_actualizacion VARCHAR2(255)
);

# Reto 02: Secuencias y Auditoría

CREATE SEQUENCE comerciante_seq START WITH 1 INCREMENT BY 1;

CREATE OR REPLACE TRIGGER trg_comerciante_auditoria
BEFORE INSERT OR UPDATE ON Comerciante
FOR EACH ROW
BEGIN
    :NEW.fecha_actualizacion := SYSDATE;
    :NEW.usuario_actualizacion := USER;
END;

# Reto 03: Datos Semilla

INSERT INTO Comerciante (nombre, departamento, municipio, telefono, correo_electronico, estado)
VALUES ('Comerciante 1', 'Departamento 1', 'Municipio 1', '123456789', 'comerciante1@example.com', 'Activo');

-- Repetir para 10 comerciantes

INSERT INTO Establecimiento (comerciante_id, nombre, ingresos, numero_empleados)
VALUES (1, 'Establecimiento 1', 1000.00, 5);

-- Repetir para 30 establecimientos

# Reto 04: Paquete CRUD para Comerciante

CREATE OR REPLACE PACKAGE comerciante_pkg AS
    PROCEDURE crear_comerciante(nombre VARCHAR2, departamento VARCHAR2, municipio VARCHAR2, telefono VARCHAR2, correo_electronico VARCHAR2, estado VARCHAR2);
    FUNCTION consultar_comerciante(id NUMBER) RETURN SYS_REFCURSOR;
    -- Otros procedimientos y funciones
END comerciante_pkg;

CREATE OR REPLACE PACKAGE BODY comerciante_pkg AS
    PROCEDURE crear_comerciante(nombre VARCHAR2, departamento VARCHAR2, municipio VARCHAR2, telefono VARCHAR2, correo_electronico VARCHAR2, estado VARCHAR2) IS
    BEGIN
        INSERT INTO Comerciante (nombre, departamento, municipio, telefono, correo_electronico, estado)
        VALUES (nombre, departamento, municipio, telefono, correo_electronico, estado);
    END crear_comerciante;

    FUNCTION consultar_comerciante(id NUMBER) RETURN SYS_REFCURSOR IS
        result_cursor SYS_REFCURSOR;
    BEGIN
        OPEN result_cursor FOR SELECT * FROM Comerciante WHERE id = id;
        RETURN result_cursor;
    END consultar_comerciante;
END comerciante_pkg;

# Reto 05: Cursor referenciado

CREATE OR REPLACE FUNCTION obtener_comerciantes_activos RETURN SYS_REFCURSOR IS
    result_cursor SYS_REFCURSOR;
BEGIN
    OPEN result_cursor FOR SELECT * FROM Comerciante WHERE estado = 'Activo';
    RETURN result_cursor;
END obtener_comerciantes_activos;

# Reto 06: Punto final de autenticación

@RestController
@RequestMapping("/api/auth")
public class AuthController {
    @PostMapping("/login")
    public ResponseEntity<?> authenticateUser (@RequestBody LoginRequest loginRequest) {
        // Lógica de autenticación y generación de JWT
    }
}

# Reto 07: Autorización por Roles

@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/api/auth/**").permitAll()
            .antMatchers("/api/admin/**").hasRole("ADMIN")
            .anyRequest().authenticated();
    }
}

# Reto 08: CRUD para Comerciante

@RestController
@RequestMapping("/api/comerciantes")
public class ComercianteController {
    @GetMapping("/{id}")
    public ResponseEntity<Comerciante> getComercianteById(@PathVariable Long id) {
        // Lógica para obtener comerciante por ID
    }

    @PostMapping
    public ResponseEntity<Comerciante> createComerciante(@RequestBody Comerciante comerciante) {
        // Lógica para crear comerciante
    }

    @PutMapping("/{id}")
    public ResponseEntity<Comerciante> updateComerciante(@PathVariable Long id, @RequestBody Comerciante comerciante) {
        // Lógica para actualizar comerciante
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteComerciante(@PathVariable Long id) {
        // Lógica para eliminar comerciante
    }
}

# Reto 09: CRUD para Establecimiento

@RestController
@RequestMapping("/api/establecimientos")
public class EstablecimientoController {
    // Similar a ComercianteController, pero para Establecimiento
}

# Reto 10: Generar archivo CSV

@GetMapping("/export/csv")
public ResponseEntity<Resource> exportToCSV() {
    // Lógica para generar y devolver el archivo CSV
}

# Reto 11: Generar archivo PDF

@GetMapping("/export/pdf/{id}")
public ResponseEntity<Resource> exportToPDF(@PathVariable Long id) {
    // Lógica para generar y devolver el archivo PDF
}

# Reto 12: Página de Iniciar Sesión en Angular

<form (ngSubmit)="onSubmit()">
    <input type="email" [(ngModel)]="email" name="email" required>
    <input type="password" [(ngModel)]="password" name="password" required>
    <input type="checkbox" [(ngModel)]="terms" name="terms" required>
    <button type="submit">Login</button>
</form>

# Reto 13: Visualizar Comerciantes

<table>
    <tr *ngFor="let comerciante of comerciantes">
        <td>{{ comerciante.nombre }}</td>
        <td>{{ comerciante.telefono }}</td>
        <td>{{ comerciante.correo_electronico }}</td>
        <td>{{ comerciante.fecha_registro }}</td>
        <td>{{ comerciante.cantidad_establecimientos }}</td>
        <td>
            <button (click)="edit(comerciante.id)">Edit</button>
            <button (click)="activate(comerciante.id)">Activate/Deactivate</button>
            <button (click)="delete(comerciante.id)">Delete</button>
            <button (click)="downloadPDF(comerciante.id)">Download PDF</button>
            <button (click)="downloadCSV()">Download CSV</button>
        </td>
    </tr>
</table>

# Reto 14: Formulario de Creación y Actualización

<form (ngSubmit)="onSubmit()">
    <input type="text" [(ngModel)]="comerciante.nombre" name="nombre" required>
    <select [(ngModel)]="comerciante.departamento" name="departamento" required>
        <option *ngFor="let dept of departamentos" [value]="dept">{{ dept }}</option>
    </select>
    <input type="text" [(ngModel)]="comerciante.telefono" name="telefono">
    <input type="email" [(ngModel)]="comerciante.correo_electronico" name="correo">
    <button type="submit">Submit</button>
</form>

git add .
git commit -m "Primer commit con los retos completados"
git push origin main
