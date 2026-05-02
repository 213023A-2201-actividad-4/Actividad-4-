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
