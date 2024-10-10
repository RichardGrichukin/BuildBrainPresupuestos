# process_image.py
from detectron2.engine import DefaultPredictor
from detectron2.config import get_cfg
from detectron2 import model_zoo
import cv2
import layoutparser as lp

def process_image(image_path):
    # Configurar Detectron2
    cfg = get_cfg()
    cfg.merge_from_file(model_zoo.get_config_file("COCO-Detection/faster_rcnn_R_50_FPN_3x.yaml"))
    cfg.MODEL.WEIGHTS = model_zoo.get_checkpoint_url("COCO-Detection/faster_rcnn_R_50_FPN_3x.yaml")
    cfg.MODEL.ROI_HEADS.SCORE_THRESH_TEST = 0.5  # Ajustar el umbral según necesidad
    cfg.MODEL.DEVICE = "cpu"  # Usar "cuda" si tienes soporte para GPU

    # Crear el predictor de Detectron2
    predictor = DefaultPredictor(cfg)

    # Leer la imagen con OpenCV
    img = cv2.imread(image_path)

    # Hacer predicciones con Detectron2
    outputs = predictor(img)

    # Usar Layout Parser para analizar las detecciones
    layout = lp.Layout([lp.TextBlock(outputs["instances"].pred_boxes[i].tensor.cpu().numpy()[0]) for i in range(len(outputs["instances"]))])

    # Extraer la cantidad de habitaciones y paredes según un criterio basado en las detecciones
    rooms = [block for block in layout if block.width > 50 and block.height > 50]  # Ejemplo de criterio para definir habitaciones
    walls = [block for block in layout if block.width < 50 and block.height > 200]  # Ejemplo de criterio para definir paredes

    # Calcular algunas métricas (puedes ajustar esto según tus necesidades)
    num_rooms = len(rooms)
    num_walls = len(walls)
    total_surface = sum([block.width * block.height for block in layout])

    return {
        "num_rooms": num_rooms,
        "num_walls": num_walls,
        "total_surface": total_surface
    }
   
