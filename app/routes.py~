from flask import Flask, request, render_template, redirect, url_for, send_file
from fpdf import FPDF
from app.scripts.process_image import process_image
import os

def init_app(app: Flask):
    @app.route('/')
    def home():
        return render_template('upload.html')

    @app.route('/upload', methods=['POST'])
    def upload():
        if 'file' not in request.files:
            return redirect(request.url)
        file = request.files['file']
        if file.filename == '':
            return redirect(request.url)
        if file:
            image_path = f'app/static/{file.filename}'
            file.save(image_path)
            processed_image = process_image(image_path)
            return redirect(url_for('home'))

    @app.route('/generate_pdf', methods=['POST'])
    def generate_pdf():
        # Supuestos de valores para el ejemplo
        num_habitaciones = 4
        superficie = 120
        num_paredes = 12
        num_ventanas = 6
        num_puertas = 3

        # Cálculo de mano de obra
        trabajadores = max(5, superficie // 60)
        dias_trabajo = max(45, superficie // 10)

        # Estimaciones de costos
        costo_por_metro_cuadrado = 500
        costo_por_ventana = 50
        costo_por_puerta = 100
        costo_por_pared = 300
        costo_mano_obra_por_trabajador = 25

        # Cálculo de costos
        costo_superficie = superficie * costo_por_metro_cuadrado
        costo_ventanas = num_ventanas * costo_por_ventana
        costo_puertas = num_puertas * costo_por_puerta
        costo_paredes = num_paredes * costo_por_pared
        costo_mano_obra = trabajadores * dias_trabajo * costo_mano_obra_por_trabajador

        costo_total = costo_superficie + costo_ventanas + costo_puertas + costo_paredes + costo_mano_obra

        # Crear el PDF
        pdf = FPDF()
        pdf.add_page()

        # Título del PDF
        pdf.set_font("Arial", size=16, style='B')
        pdf.cell(0, 10, "Presupuesto de Construcción", 0, 1, 'C')
        pdf.ln(10)

        # Detalles de la construcción en tabla
        pdf.set_font("Arial", size=12, style='B')
        pdf.set_fill_color(200, 220, 255)
        pdf.cell(60, 10, "Parámetro", 1, 0, 'C', True)
        pdf.cell(60, 10, "Valor", 1, 1, 'C', True)

        pdf.set_font("Arial", size=12)
        pdf.cell(60, 10, "Numero de Habitaciones", 1, 0, 'C')
        pdf.cell(60, 10, f"{num_habitaciones}", 1, 1, 'C')

        pdf.cell(60, 10, "Superficie Total", 1, 0, 'C')
        pdf.cell(60, 10, f"{superficie} m²", 1, 1, 'C')

        pdf.cell(60, 10, "Numero de Paredes", 1, 0, 'C')
        pdf.cell(60, 10, f"{num_paredes}", 1, 1, 'C')

        pdf.cell(60, 10, "Numero de Ventanas", 1, 0, 'C')
        pdf.cell(60, 10, f"{num_ventanas}", 1, 1, 'C')

        pdf.cell(60, 10, "Numero de Puertas", 1, 0, 'C')
        pdf.cell(60, 10, f"{num_puertas}", 1, 1, 'C')

        pdf.ln(10)

        # Mano de obra
        pdf.set_font("Arial", size=12, style='B')
        pdf.set_fill_color(200, 220, 255)
        pdf.cell(60, 10, "Detalles de Mano de Obra", 1, 0, 'C', True)
        pdf.cell(60, 10, "Valor", 1, 1, 'C', True)

        pdf.set_font("Arial", size=12)
        pdf.cell(60, 10, "Cantidad de Trabajadores", 1, 0, 'C')
        pdf.cell(60, 10, f"{trabajadores}", 1, 1, 'C')

        pdf.cell(60, 10, "Días de Trabajo", 1, 0, 'C')
        pdf.cell(60, 10, f"{dias_trabajo} días", 1, 1, 'C')

        pdf.ln(10)

        # Detalle de costos en formato tabular
        pdf.set_font("Arial", size=12, style='B')
        pdf.set_fill_color(200, 220, 255)
        pdf.cell(100, 10, "Concepto", 1, 0, 'C', True)
        pdf.cell(40, 10, "Cantidad", 1, 0, 'C', True)
        pdf.cell(40, 10, "Costo (USD)", 1, 1, 'C', True)

        pdf.set_font("Arial", size=12)
        pdf.cell(100, 10, f"Costo por Superficie (${costo_por_metro_cuadrado}/m²)", 1, 0, 'C')
        pdf.cell(40, 10, f"{superficie} m²", 1, 0, 'C')
        pdf.cell(40, 10, f"${costo_superficie}", 1, 1, 'C')

        pdf.cell(100, 10, f"Costo por Ventanas (${costo_por_ventana} cada una)", 1, 0, 'C')
        pdf.cell(40, 10, f"{num_ventanas} ventanas", 1, 0, 'C')
        pdf.cell(40, 10, f"${costo_ventanas}", 1, 1, 'C')

        pdf.cell(100, 10, f"Costo por Puertas (${costo_por_puerta} cada una)", 1, 0, 'C')
        pdf.cell(40, 10, f"{num_puertas} puertas", 1, 0, 'C')
        pdf.cell(40, 10, f"${costo_puertas}", 1, 1, 'C')

        pdf.cell(100, 10, f"Costo por Paredes (${costo_por_pared} cada una)", 1, 0, 'C')
        pdf.cell(40, 10, f"{num_paredes} paredes", 1, 0, 'C')
        pdf.cell(40, 10, f"${costo_paredes}", 1, 1, 'C')

        pdf.cell(100, 10, f"Costo de Mano de Obra ({dias_trabajo} días a ${costo_mano_obra_por_trabajador}/día)", 1, 0, 'C')
        pdf.cell(40, 10, f"{trabajadores} trabajadores", 1, 0, 'C')
        pdf.cell(40, 10, f"${costo_mano_obra}", 1, 1, 'C')

        pdf.ln(10)

        # Costo total
        pdf.set_font("Arial", size=14, style='B')
        pdf.cell(140, 10, "Costo Total Estimado:", 1, 0, 'C', True)
        pdf.cell(40, 10, f"${costo_total} USD", 1, 1, 'C', True)

        # Guardar PDF
        pdf_output_path = "presupuesto_construccion.pdf"
        pdf.output(pdf_output_path)

        return send_file(pdf_output_path, as_attachment=True)
       


