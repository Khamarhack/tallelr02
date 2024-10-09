#include <iostream>
#include <fstream>
#include <string>
#include <vector>
#include <algorithm>


struct Cancion {
    std::string nombre;
    std::string autor;
    std::string genero;
    std::string album;
    int año;
};


std::vector<Cancion> leerCanciones(const std::string& nombreArchivo) {
    std::ifstream archivo(nombreArchivo);
    std::vector<Cancion> canciones;
    std::string linea;
    if (archivo.is_open()) {
        int numCanciones;
        std::getline(archivo, linea);
        numCanciones = std::stoi(linea);
        for (int i = 0; i < numCanciones; ++i) {
            Cancion cancion;
            std::getline(archivo, cancion.nombre, '|');
            std::getline(archivo, cancion.autor, '|');
            std::getline(archivo, cancion.genero, '|');
            std::getline(archivo, cancion.album, '|');
            std::getline(archivo, linea);
            cancion.año = std::stoi(linea);
            canciones.push_back(cancion);
        }
        archivo.close();
    } else {
        std::cout << "No se pudo abrir el archivo.\n";
    }
    return canciones;
}


void listarAutores(const std::vector<Cancion>& canciones) {
    std::vector<std::string> autores;
    for (const auto& cancion : canciones) {
        autores.push_back(cancion.autor);
    }
    std::sort(autores.begin(), autores.end());
    autores.erase(std::unique(autores.begin(), autores.end()), autores.end());
    std::cout << "Autores:\n";
    for (const auto& autor : autores) {
        std::cout << autor << "\n";
    }
}


void listarCancionesPorAutor(const std::vector<Cancion>& canciones, const std::string& autor) {
    std::vector<Cancion> cancionesAutor;
    for (const auto& cancion : canciones) {
        if (cancion.autor == autor) {
            cancionesAutor.push_back(cancion);
        }
    }
    std::sort(cancionesAutor.begin(), cancionesAutor.end(), [](const Cancion& a, const Cancion& b) {
        return a.nombre < b.nombre;
    });
    std::cout << "Canciones de " << autor << ":\n";
    for (const auto& cancion : cancionesAutor) {
        std::cout << cancion.nombre << "\n";
    }
}


void listarAlbumes(const std::vector<Cancion>& canciones) {
    std::vector<std::pair<std::string, int>> albumes;
    for (const auto& cancion : canciones) {
        albumes.push_back({cancion.album, cancion.año});
    }
    std::sort(albumes.begin(), albumes.end(), [](const auto& a, const auto& b) {
        return a.second < b.second;
    });
    albumes.erase(std::unique(albumes.begin(), albumes.end()), albumes.end());
    std::cout << "Álbumes:\n";
    for (const auto& album : albumes) {
        std::cout << album.first << " (" << album.second << ")\n";
    }
}


void listarCancionesPorAlbum(const std::vector<Cancion>& canciones, const std::string& album) {
    std::vector<Cancion> cancionesAlbum;
    for (const auto& cancion : canciones) {
        if (cancion.album == album) {
            cancionesAlbum.push_back(cancion);
        }
    }
    std::sort(cancionesAlbum.begin(), cancionesAlbum.end(), [](const Cancion& a, const Cancion& b) {
        return a.nombre < b.nombre;
    });
    std::cout << "Canciones en el álbum " << album << ":\n";
    for (const auto& cancion : cancionesAlbum) {
        std::cout << cancion.nombre << "\n";
    }
}


void listarCancionesPorAlbumYNombre(const std::vector<Cancion>& canciones) {
    std::vector<Cancion> cancionesOrdenadas = canciones;
    std::sort(cancionesOrdenadas.begin(), cancionesOrdenadas.end(), [](const Cancion& a, const Cancion& b) {
        if (a.album == b.album) {
            return a.nombre < b.nombre;
        }
        return a.album < b.album;
    });
    std::cout << "Canciones ordenadas por álbum y nombre:\n";
    for (const auto& cancion : cancionesOrdenadas) {
        std::cout << cancion.album << " - " << cancion.nombre << "\n";
    }
}


void mostrarMenu() {
    std::cout << "1. Leer archivo de canciones\n";
    std::cout << "2. Listar autores\n";
    std::cout << "3. Buscar canciones por autor\n";
    std::cout << "4. Listar álbumes\n";
    std::cout << "5. Buscar canciones por álbum\n";
    std::cout << "6. Listar todas las canciones por álbum y nombre\n";
    std::cout << "7. Salir\n";
}


int main() {
    std::vector<Cancion> canciones;
    int opcion;
    std::string entrada;

    do {
        mostrarMenu();
        std::cout << "Seleccione una opción: ";
        std::cin >> opcion;

        switch (opcion) {
            case 1:
                std::cout << "Ingrese el nombre del archivo: ";
                std::cin >> entrada;
                canciones = leerCanciones(entrada);
                break;
            case 2:
                listarAutores(canciones);
                break;
            case 3:
                std::cout << "Ingrese el nombre del autor: ";
                std::cin.ignore();
                std::getline(std::cin, entrada);
                listarCancionesPorAutor(canciones, entrada);
                break;
            case 4:
                listarAlbumes(canciones);
                break;
            case 5:
                std::cout << "Ingrese el nombre del álbum: ";
                std::cin.ignore();
                std::getline(std::cin, entrada);
                listarCancionesPorAlbum(canciones, entrada);
                break;
            case 6:
                listarCancionesPorAlbumYNombre(canciones);
                break;
            case 7:
                std::cout << "Saliendo...\n";
                break;
            default:
                std::cout << "Opción no válida. Intente de nuevo.\n";
        }
    } while (opcion != 7);

    return 0;
}
