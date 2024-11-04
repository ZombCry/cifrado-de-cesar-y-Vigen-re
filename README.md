#Importar Libreria tkinter y messagebox
import tkinter as tk
from tkinter import messagebox

# Función que realiza el cifrado o descifrado del Cifrado César
def ejecutar_accion(texto, desplazamiento, accion):
    texto = texto.upper()
    resultado = ""

    for letra in texto:
        if letra.isalpha():
            if accion == "Cifrar":
                nueva_letra = chr((ord(letra) - 65 + desplazamiento) % 26 + 65)
            else:
                nueva_letra = chr((ord(letra) - 65 - desplazamiento) % 26 + 65)
            resultado += nueva_letra
        else:
            resultado += letra

    resultado_texto.config(state=tk.NORMAL)
    resultado_texto.delete(1.0, tk.END)
    resultado_texto.insert(tk.END, resultado)
    resultado_texto.config(state=tk.DISABLED)
    btn_copiar.config(state=tk.NORMAL)

# Función para el cifrado y descifrado de Vigenère
def ejecutar_vigenere(texto, clave, accion):
    texto = texto.upper()
    clave = clave.upper()
    resultado = ""
    indice_clave = 0

    for letra in texto:
        if letra.isalpha():
            desplazamiento = ord(clave[indice_clave % len(clave)]) - 65
            if accion == "Cifrar":
                nueva_letra = chr((ord(letra) - 65 + desplazamiento) % 26 + 65)
            else:
                nueva_letra = chr((ord(letra) - 65 - desplazamiento) % 26 + 65)
            resultado += nueva_letra
            indice_clave += 1
        else:
            resultado += letra

    resultado_texto_vigenere.config(state=tk.NORMAL)
    resultado_texto_vigenere.delete(1.0, tk.END)
    resultado_texto_vigenere.insert(tk.END, resultado)
    resultado_texto_vigenere.config(state=tk.DISABLED)
    btn_copiar_vigenere.config(state=tk.NORMAL)

# Función para copiar el texto cifrado o descifrado al portapapeles
def copiar_al_portapapeles(texto_widget):
    root.clipboard_clear()
    root.clipboard_append(texto_widget.get(1.0, tk.END).strip())
    messagebox.showinfo("Copiado", "El texto ha sido copiado al portapapeles.")

# Función para abrir la ventana de la tabla de Vigenère
def abrir_tabla_vigenere():
    ventana_tabla = tk.Toplevel(root)
    ventana_tabla.title("Tabla de Vigenère")
    ventana_tabla.geometry("500x700")
    ventana_tabla.configure(bg="#e0f7fa")

    # Crear la tabla de Vigenère
    tabla_vigenere = tk.Frame(ventana_tabla, bg="#e0f7fa")
    tabla_vigenere.pack(padx=10, pady=10)

    alfabeto = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    for i in range(26):
        for j in range(26):
            letra = chr((i + j) % 26 + 65)
            celda = tk.Label(tabla_vigenere, text=letra, width=2, height=1, font=("Helvetica", 10), bg="white", relief="solid")
            celda.grid(row=i, column=j, padx=1, pady=1)
        # Encabezados de la tabla
    for k in range(26):
        encabezado_fila = tk.Label(tabla_vigenere, text=alfabeto[k], width=2, height=1, font=("Helvetica", 10, "bold"), bg="#c5cae9")
        encabezado_columna = tk.Label(tabla_vigenere, text=alfabeto[k], width=2, height=1, font=("Helvetica", 10, "bold"), bg="#c5cae9")
        encabezado_fila.grid(row=k+1, column=0, padx=1, pady=1)
        encabezado_columna.grid(row=0, column=k+1, padx=1, pady=1)

# Función para abrir el menú de Cifrado César
def cifrado_cesar():
    root.withdraw()
    ventana_cesar = tk.Toplevel(root)
    ventana_cesar.title("Cifrado César")
    ventana_cesar.geometry("500x500")
    ventana_cesar.configure(bg="#e0f7fa")

    label_cesar = tk.Label(ventana_cesar, text="Cifrado César", bg="#e0f7fa", font=("Helvetica", 16, "bold"))
    label_cesar.pack(pady=10)
    entrada_texto = tk.Entry(ventana_cesar, width=40, font=("Helvetica", 12))
    entrada_texto.pack(pady=10)  
    
    desplazamiento_var = tk.IntVar(value=1)
    desplazamiento_label = tk.Label(ventana_cesar, text="Desplazamiento:", bg="#e0f7fa", font=("Helvetica", 12))
    desplazamiento_label.pack(pady=5)
    desplazamiento_menu = tk.OptionMenu(ventana_cesar, desplazamiento_var, *range(1, 16))
    desplazamiento_menu.config(font=("Helvetica", 12), bg="#ffffff")
    desplazamiento_menu.pack(pady=5)

    accion_var = tk.StringVar(value="Cifrar")
    accion_label = tk.Label(ventana_cesar, text="Acción:", bg="#e0f7fa", font=("Helvetica", 12))
    accion_label.pack(pady=5)
    accion_menu = tk.OptionMenu(ventana_cesar, accion_var, "Cifrar", "Descifrar")
    accion_menu.config(font=("Helvetica", 12), bg="#ffffff")
    accion_menu.pack(pady=5)

    btn_cifrar = tk.Button(ventana_cesar, text="Ejecutar", 
                           command=lambda: ejecutar_accion(entrada_texto.get(), desplazamiento_var.get(), accion_var.get()), 
                           bg="#4CAF50", fg="white", font=("Helvetica", 12, "bold"))
    btn_cifrar.pack(pady=10)

    global resultado_texto, btn_copiar
    resultado_texto = tk.Text(ventana_cesar, height=4, width=40, font=("Helvetica", 12), state=tk.DISABLED)
    resultado_texto.pack(pady=5)
    btn_copiar = tk.Button(ventana_cesar, text="Copiar al Portapapeles", command=lambda: copiar_al_portapapeles(resultado_texto),
                           bg="#2196F3", fg="white", font=("Helvetica", 12, "bold"), state=tk.DISABLED)
    btn_copiar.pack(pady=5)

    btn_volver = tk.Button(ventana_cesar, text="Volver", command=lambda: volver_menu(ventana_cesar), 
                           bg="#f44336", fg="white", font=("Helvetica", 12, "bold"))
    btn_volver.pack(pady=10)

# Función para el menú de Cifrado Vigenère
def cifrado_vigenere():
    root.withdraw()
    ventana_vigenere = tk.Toplevel(root)
    ventana_vigenere.title("Cifrado Vigenère")
    ventana_vigenere.geometry("400x500")
    ventana_vigenere.configure(bg="#e0f7fa")

    # Ajuste para que la ventana sea responsiva
    ventana_vigenere.grid_rowconfigure(0, weight=1)
    ventana_vigenere.grid_rowconfigure(1, weight=1)
    ventana_vigenere.grid_rowconfigure(2, weight=1)
    ventana_vigenere.grid_rowconfigure(3, weight=1)
    ventana_vigenere.grid_rowconfigure(4, weight=1)
    ventana_vigenere.grid_rowconfigure(5, weight=1)
    
    label_vigenere = tk.Label(ventana_vigenere, text="Cifrado Vigenère", bg="#e0f7fa", font=("Helvetica", 16, "bold"))
    label_vigenere.grid(row=0, column=0, padx=10, pady=10)

    entrada_texto_vigenere = tk.Entry(ventana_vigenere, font=("Helvetica", 12), width=40)
    entrada_texto_vigenere.grid(row=1, column=0, padx=10, pady=5)  

    clave_label = tk.Label(ventana_vigenere, text="Clave:", bg="#e0f7fa", font=("Helvetica", 12))
    clave_label.grid(row=2, column=0, padx=10, pady=5)
    
    entrada_clave = tk.Entry(ventana_vigenere, font=("Helvetica", 12), width=20)
    entrada_clave.grid(row=3, column=0, padx=10, pady=5)

    accion_var_vigenere = tk.StringVar(value="Cifrar")
    accion_label_vigenere = tk.Label(ventana_vigenere, text="Acción:", bg="#e0f7fa", font=("Helvetica", 12))
    accion_label_vigenere.grid(row=4, column=0, padx=10, pady=5)
    
    accion_menu_vigenere = tk.OptionMenu(ventana_vigenere, accion_var_vigenere, "Cifrar", "Descifrar")
    accion_menu_vigenere.config(font=("Helvetica", 12), bg="#ffffff")
    accion_menu_vigenere.grid(row=5, column=0, padx=10, pady=5)

    btn_cifrar_vigenere = tk.Button(ventana_vigenere, text="Ejecutar",
                                    command=lambda: ejecutar_vigenere(entrada_texto_vigenere.get(), entrada_clave.get(), accion_var_vigenere.get()),
                                    bg="#4CAF50", fg="white", font=("Helvetica", 12, "bold"))
    btn_cifrar_vigenere.grid(row=6, column=0, padx=10, pady=10)

    global resultado_texto_vigenere, btn_copiar_vigenere
    resultado_texto_vigenere = tk.Text(ventana_vigenere, height=4, width=40, font=("Helvetica", 12), state=tk.DISABLED)
    resultado_texto_vigenere.grid(row=7, column=0, padx=10, pady=5)
    
    btn_copiar_vigenere = tk.Button(ventana_vigenere, text="Copiar al Portapapeles",
                                    command=lambda: copiar_al_portapapeles(resultado_texto_vigenere),
                                    bg="#2196F3", fg="white", font=("Helvetica", 12, "bold"), state=tk.DISABLED)
    btn_copiar_vigenere.grid(row=8, column=0, padx=10, pady=5)

    btn_tabla_vigenere = tk.Button(ventana_vigenere, text="Ver Tabla de Vigenère", command=abrir_tabla_vigenere,
                                   bg="#ff9800", fg="white", font=("Helvetica", 12, "bold"))
    btn_tabla_vigenere.grid(row=9, column=0, padx=10, pady=10)

    btn_volver_vigenere = tk.Button(ventana_vigenere, text="Volver", command=lambda: volver_menu(ventana_vigenere), 
                                    bg="#f44336", fg="white", font=("Helvetica", 12, "bold"))
    btn_volver_vigenere.grid(row=10, column=0, padx=10, pady=10)
# Función para regresar al menú principal
def volver_menu(ventana):
    ventana.destroy()
    root.deiconify()

# Función para salir del programa
def salir():
    messagebox.showinfo("Salir", "Gracias por visitar nuestro cifrado, vuelve pronto.")
    root.quit()

# Crear la ventana principal
root = tk.Tk()
root.title("Cifrado de César y Vigenère")
root.geometry("450x300")
root.configure(bg="#e0f7fa")
root.protocol("WM_DELETE_WINDOW", salir)

# Elementos en la ventana principal
label_bienvenida = tk.Label(root, text="Bienvenidos al mejor cifrado", bg="#e0f7fa", font=("Helvetica", 16, "bold"))
label_bienvenida.pack(pady=10)
label_desarrollador = tk.Label(root, text="Programa desarrollado por el Ing. Johan Sebastián Rodríguez G", 
                               bg="#e0f7fa", font=("Helvetica", 10, "italic", "bold"), wraplength=350)
label_desarrollador.pack(pady=5)
btn_cesar = tk.Button(root, text="Cifrado César", command=cifrado_cesar, 
                      bg="#4CAF50", fg="white", font=("Helvetica", 12, "bold"), width=20)
btn_cesar.pack(pady=10)
btn_vigenere = tk.Button(root, text="Cifrado Vigenère", command=cifrado_vigenere, 
                         bg="#2196F3", fg="white", font=("Helvetica", 12, "bold"), width=20)
btn_vigenere.pack(pady=10)
btn_salir = tk.Button(root, text="Salir", command=salir, 
                      bg="#f44336", fg="white", font=("Helvetica", 12, "bold"), width=20)
btn_salir.pack(pady=10)

# Iniciar el bucle principal
root.mainloop()
