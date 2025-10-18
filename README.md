# Delícias da Rosa — App de Vendas (PySimpleGUI)

Projeto exemplo de uma aplicação desktop com interface gráfica em Python usando **PySimpleGUI**.
Esta entrega serve como base para o trabalho.

## O que está neste repositório
- `main.py` — aplicação desktop (vendas simples).
- `requirements.txt` — dependências.
- `orders/` — pasta onde pedidos salvos (.csv) serão gravados (criada ao salvar).
- `screenshot_interface.png` — print de exemplo da interface (mockup).
- `README.md` — este arquivo.

## Instruções para execução (local)
1. Crie um ambiente virtual recomendado:
```bash
python -m venv .venv
source .venv/bin/activate   # Linux / macOS
.venv\Scripts\activate      # Windows PowerShell
```
2. Instale dependências:
```bash
pip install -r requirements.txt
```
3. Execute:
```bash
python main.py
```

## Publicar no GitHub (passo a passo rápido)
1. Crie um repositório no GitHub (ex.: `delicias-da-rosa-app`).
2. Inicialize git e faça commit:
```bash
git init
git add .
git commit -m "Initial commit - Delícias da Rosa PySimpleGUI app"
git branch -M main
git remote add origin https://github.com/SEU_USUARIO/REPO.git
git push -u origin main
```
3. No README do GitHub, inclua os nomes do grupo (até 6) e instruções de execução.
4. Faça upload do `screenshot_interface.png` na raiz do repositório.

## Entrega individual
Cada integrante deve:
- Enviar o **link do repositório** público no GitHub.
- Incluir um **print** da interface (o `screenshot_interface.png` já está incluso).
- Enviar um **ZIP** do repositório (ex.: `delicias-da-rosa_app.zip`) — gerado nesta pasta.
