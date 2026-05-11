# ==========================================================
# PROYECTO: SOFTWARE FJ
# Sistema integral orientado a objetos sin base de datos
# Lenguaje: Python
# ==========================================================

from abc import ABC, abstractmethod
from datetime import datetime


# ==========================================================
# LOGS
# ==========================================================

LOG_FILE = "logs_software_fj.txt"


def registrar_log(mensaje):
    with open(LOG_FILE, "a", encoding="utf-8") as archivo:
        archivo.write(f"{datetime.now()} - {mensaje}\n")


# ==========================================================
# EXCEPCIONES PERSONALIZADAS
# ==========================================================

class ClienteError(Exception):
    pass


class ServicioError(Exception):
    pass


class ReservaError(Exception):
    pass


# ==========================================================
# CLASE ABSTRACTA GENERAL
# ==========================================================

class Entidad(ABC):

    @abstractmethod
    def mostrar_info(self):
        pass


# ==========================================================
# CLIENTE
# ==========================================================

class Cliente(Entidad):

    def __init__(self, nombre, documento, correo):
        try:
            self.__nombre = nombre.strip()
            self.__documento = documento.strip()
            self.__correo = correo.strip()

            if not self.__nombre:
                raise ClienteError("Nombre vacío")

            if not self.__documento.isdigit():
                raise ClienteError("Documento inválido")

            if "@" not in self.__correo:
                raise ClienteError("Correo inválido")

            registrar_log(f"Cliente registrado: {self.__nombre}")

        except Exception as e:
            registrar_log(f"Error creando cliente: {e}")
            raise

    # Encapsulación
    @property
    def nombre(self):
        return self.__nombre

    @property
    def documento(self):
        return self.__documento

    @property
    def correo(self):
        return self.__correo

    def mostrar_info(self):
        return f"Cliente: {self.__nombre} - Doc: {self.__documento}"


# ==========================================================
# SERVICIO ABSTRACTO
# ==========================================================

class Servicio(ABC):

    def __init__(self, nombre, tarifa):
        if tarifa <= 0:
            raise ServicioError("Tarifa inválida")
        self.nombre = nombre
        self.tarifa = tarifa

    @abstractmethod
    def calcular_costo(self, horas):
        pass

    @abstractmethod
    def descripcion(self):
        pass


# ==========================================================
# SERVICIOS ESPECIALIZADOS
# ==========================================================

class ReservaSala(Servicio):

    def calcular_costo(self, horas):
        return self.tarifa * horas

    def descripcion(self):
        return "Reserva de sala empresarial"


class AlquilerEquipo(Servicio):

    def calcular_costo(self, horas):
        seguro = 20
        return (self.tarifa * horas) + seguro

    def descripcion(self):
        return "Alquiler de equipos tecnológicos"


class AsesoriaEspecializada(Servicio):

    def calcular_costo(self, horas):
        recargo = 50
        return (self.tarifa * horas) + recargo

    def descripcion(self):
        return "Asesoría profesional especializada"


# ==========================================================
# RESERVA
# ==========================================================

class Reserva:

    def __init__(self, cliente, servicio, horas):
        try:
            if not isinstance(cliente, Cliente):
                raise ReservaError("Cliente inválido")

            if not isinstance(servicio, Servicio):
                raise ReservaError("Servicio inválido")

            if horas <= 0:
                raise ReservaError("Horas inválidas")

            self.cliente = cliente
            self.servicio = servicio
            self.horas = horas
            self.estado = "Pendiente"

            registrar_log("Reserva creada correctamente")

        except Exception as e:
            registrar_log(f"Error en reserva: {e}")
            raise

    def confirmar(self):
        self.estado = "Confirmada"
        registrar_log("Reserva confirmada")

    def cancelar(self):
        self.estado = "Cancelada"
        registrar_log("Reserva cancelada")

    # Sobrecarga simulada con parámetros opcionales
    def procesar_pago(self, impuesto=0, descuento=0):
        try:
            costo = self.servicio.calcular_costo(self.horas)

            costo += costo * impuesto
            costo -= costo * descuento

            return round(costo, 2)

        except Exception as e:
            registrar_log(f"Error procesando pago: {e}")
            raise ReservaError("No se pudo procesar pago") from e

    def mostrar(self):
        print("--------------------------------")
        print(self.cliente.mostrar_info())
        print("Servicio:", self.servicio.nombre)
        print("Estado:", self.estado)
        print("--------------------------------")


# ==========================================================
# SISTEMA GENERAL
# ==========================================================

clientes = []
reservas = []


def simular_operaciones():

    print("===== INICIANDO SISTEMA SOFTWARE FJ =====")

    operaciones = [

        # Clientes válidos
        ("cliente", "Miguel", "12345", "miguel@gmail.com"),
        ("cliente", "Ana", "54321", "ana@hotmail.com"),

        # Cliente inválido
        ("cliente", "", "ABC", "correo"),

        # Servicios válidos
        ("reserva", 0),
        ("reserva", 1),

        # Reserva inválida
        ("reserva_error", None),

    ]

    # Crear clientes
    for op in operaciones:

        try:
            if op[0] == "cliente":
                c = Cliente(op[1], op[2], op[3])
                clientes.append(c)

        except Exception as e:
            print("Error cliente:", e)

    # Crear servicios
    servicios = []

    try:
        servicios.append(ReservaSala("Sala Premium", 100))
        servicios.append(AlquilerEquipo("Portátil Gamer", 80))
        servicios.append(AsesoriaEspecializada("Consultoría IA", 150))

    except Exception as e:
        print("Error servicio:", e)

    # 10 operaciones completas
    for i in range(10):

        try:

            cliente = clientes[i % len(clientes)]
            servicio = servicios[i % len(servicios)]
            horas = i + 1

            reserva = Reserva(cliente, servicio, horas)

            if i % 2 == 0:
                reserva.confirmar()
            else:
                reserva.cancelar()

            total = reserva.procesar_pago(
                impuesto=0.19,
                descuento=0.05
            )

            reserva.mostrar()
            print("Total:", total)

            reservas.append(reserva)

        except Exception as e:
            print("Error operación:", e)

    # Error grave controlado
    try:
        r = Reserva("cliente falso", servicios[0], -5)

    except Exception as e:
        print("Error grave controlado:", e)

    finally:
        registrar_log("Simulación finalizada")

    print("===== SISTEMA FINALIZADO SIN CAER =====")


# ==========================================================
# MAIN
# ==========================================================

if __name__ == "__main__":
    simular_operaciones()
