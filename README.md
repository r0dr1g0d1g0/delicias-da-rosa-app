App de vendas - "Delícias da Rosa" 
Interface em PySimpleGUI: listar produtos, adicionar ao carrinho, ver total e salvar pedido.
"""

import PySimpleGUI as sg
import csv
from datetime import datetime
import os

PRODUCTS = [
    {"id": 1, "name": "Brigadeiro (un)", "price": 2.50},
    {"id": 2, "name": "Bolo de Cenoura (fatia)", "price": 6.00},
    {"id": 3, "name": "Torta de Maçã (fatia)", "price": 7.50},
    {"id": 4, "name": "Cookie (un)", "price": 3.00},
    {"id": 5, "name": "Cupcake (un)", "price": 4.50},
]

sg.theme("LightBlue3")

product_names = [f'{p["id"]} - {p["name"]} — R$ {p["price"]:.2f}' for p in PRODUCTS]

layout = [
    [sg.Text("Delícias da Rosa — Vendas", font=("Helvetica", 20), justification="center", expand_x=True)],
    [sg.Frame("Produtos", [[
        sg.Listbox(values=product_names, size=(40,6), key="-PRODUCT_LIST-", enable_events=True),
        sg.Column([
            [sg.Text("Quantidade:"), sg.Spin([i for i in range(1,51)], initial_value=1, key="-QTD-")],
            [sg.Button("Adicionar ao carrinho", key="-ADD-")],
            [sg.Button("Remover selecionado", key="-REMOVE-")],
        ])
    ]])],
    [sg.Frame("Carrinho", [[
        sg.Listbox(values=[], size=(60,8), key="-CART-"),
        [sg.Text("Total: R$ ", pad=(0,0)), sg.Text("0.00", key="-TOTAL-")]
    ]])],
    [sg.Multiline(size=(80,5), key="-LOG-", disabled=True)],
    [sg.Button("Salvar pedido (.csv)"), sg.Button("Limpar"), sg.Button("Sair")]
]

window = sg.Window("Delícias da Rosa - Vendas", layout, finalize=True)

cart = []

def recalc_total():
    total = sum(item["price"] * item["qty"] for item in cart)
    window["-TOTAL-"].update(f"{total:.2f}")
    window["-CART-"].update([f'{i+1}. {c["name"]} x{c["qty"]} — R$ {c["price"]:.2f} = R$ {c["price"]*c["qty"]:.2f}' for i,c in enumerate(cart)])

def add_to_cart(selected_index, qty):
    prod = PRODUCTS[selected_index]
    # if same product exists, increment qty
    for item in cart:
        if item["id"] == prod["id"]:
            item["qty"] += qty
            return
    cart.append({"id": prod["id"], "name": prod["name"], "price": prod["price"], "qty": qty})

def save_order():
    if not cart:
        sg.popup("Carrinho vazio. Nada para salvar.")
        return
    os.makedirs("orders", exist_ok=True)
    filename = f'orders/order_{datetime.now().strftime("%Y%m%d_%H%M%S")}.csv'
    with open(filename, "w", newline="", encoding="utf-8") as f:
        writer = csv.writer(f)
        writer.writerow(["Produto", "Quantidade", "Preço unitário", "Subtotal"])
        for item in cart:
            writer.writerow([item["name"], item["qty"], f"{item['price']:.2f}", f"{item['price']*item['qty']:.2f}"])
        total = sum(item["price"]*item["qty"] for item in cart)
        writer.writerow([])
        writer.writerow(["Total", "", "", f"{total:.2f}"])
    window["-LOG-"].print(f"Pedido salvo em: {filename}")
    sg.popup("Pedido salvo", f"Arquivo gerado:\n{filename}")

while True:
    event, values = window.read()
    if event == sg.WIN_CLOSED or event == "Sair":
        break
    if event == "-ADD-":
        sel = values["-PRODUCT_LIST-"]
        if not sel:
            sg.popup("Selecione um produto antes de adicionar.")
            continue
        # find product index by the selected string (starts with "id -")
        selected_text = sel[0]
        selected_id = int(selected_text.split("-")[0].strip())
        selected_index = next((i for i,p in enumerate(PRODUCTS) if p["id"]==selected_id), None)
        qty = int(values["-QTD-"])
        add_to_cart(selected_index, qty)
        recalc_total()
        window["-LOG-"].print(f'Adicionado: {PRODUCTS[selected_index]["name"]} x{qty}')
    if event == "-REMOVE-":
        sel = values["-CART-"]
        if not sel:
            sg.popup("Selecione um item no carrinho para remover.")
            continue
        # remove by list index (string formatted as "1. name x..")
        selected_str = sel[0]
        idx = int(selected_str.split(".")[0]) - 1
        if 0 <= idx < len(cart):
            removed = cart.pop(idx)
            window["-LOG-"].print(f'Removido: {removed["name"]}')
            recalc_total()
    if event == "Salvar pedido (.csv)":
        save_order()
    if event == "Limpar":
        cart.clear()
        recalc_total()
        window["-LOG-"].print("Carrinho limpo.")

window.close()

