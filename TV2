import re
from pathlib import Path
import logging
import sys

# Настройка логирования (только консоль)
logging.basicConfig(
    level=logging.INFO,  # Можно изменить на DEBUG для более подробного логирования
    format='%(asctime)s [%(levelname)s] %(message)s',
    handlers=[
        logging.StreamHandler(sys.stdout)
    ]
)

# Заданные директории
DIRECTORIES = [
    Path(r"C:\Share\TV\new\AutoPlayH"),
    Path(r"C:\Share\TV\new\AutoPlayV")
]

def process_export_playlist(directory):
    """
    Обрабатывает файл ExportPlaylist.cpls в указанной директории: заменяет 'pcts' на 'xml'.

    :param directory: Путь к директории, содержащей файл ExportPlaylist.cpls
    """
    playlist_path = directory / "ExportPlaylist.cpls"
    if not playlist_path.exists():
        logging.error(f"Файл {playlist_path} не найден.")
        return

    try:
        content = playlist_path.read_text(encoding="utf-8")
        updated_content = content.replace("pcts", "xml")
        playlist_path.write_text(updated_content, encoding="utf-8")
        
        logging.info(f"Файл {playlist_path} успешно обработан.")
    except Exception as e:
        logging.error(f"Ошибка при обработке файла {playlist_path}: {e}")

def rename_pcts_to_xml(directory):
    """
    Переименовывает все файлы с расширением .pcts в .xml в указанной директории.

    :param directory: Путь к директории для переименования файлов
    """
    pcts_files = list(directory.glob("*.pcts"))
    if not pcts_files:
        logging.info(f"Нет файлов с расширением .pcts для переименования в директории {directory}.")
        return

    for pcts_file in pcts_files:
        xml_file = pcts_file.with_suffix(".xml")
        try:
            pcts_file.rename(xml_file)
            logging.info(f"Файл {pcts_file} переименован в {xml_file}.")
        except Exception as e:
            logging.error(f"Ошибка при переименовании файла {pcts_file}: {e}")

def process_xml_file(file_path):
    """
    Обрабатывает отдельный XML файл с указанными заменами.

    :param file_path: Путь к XML файлу
    """
    try:
        content = file_path.read_text(encoding="utf-8")
        
        # Операция 2: Замена строки <Canvas ...> с сохранением значения Idx
        # Упрощенное и более гибкое регулярное выражение
        canvas_pattern = re.compile(
            r'<Canvas[^>]*Duration="60"[^>]*Idx="(\d+)"[^>]*>',
            re.IGNORECASE
        )
        canvas_replacement = r'<Canvas Duration="15" Idx="\g<1>" PlayerType="Monitor" Type="NULLCANVAS">'
        content, canvas_subs = canvas_pattern.subn(canvas_replacement, content)
        if canvas_subs > 0:
            logging.debug(f"Заменено {canvas_subs} в Canvas в файле {file_path}")
        else:
            logging.debug(f"Не найдено соответствий для Canvas в файле {file_path}")

        # Операция 3: Изменение значения Duration в <Content ...>
        content_pattern = re.compile(r'(<Content\s+Duration=")60(".*?>)', re.IGNORECASE)
        content_replacement = r'\g<1>15\2'
        content, content_subs = content_pattern.subn(content_replacement, content)
        if content_subs > 0:
            logging.debug(f"Заменено {content_subs} в Content в файле {file_path}")
        else:
            logging.debug(f"Не найдено соответствий для Content в файле {file_path}")
        
        # Записываем изменения обратно в файл
        file_path.write_text(content, encoding="utf-8")
        
        logging.info(f"Файл {file_path} успешно обработан.")
    except Exception as e:
        logging.error(f"Ошибка при обработке файла {file_path}: {e}")

def process_xml_files(directory):
    """
    Обрабатывает все XML файлы в указанной директории.

    :param directory: Путь к директории с XML файлами
    """
    xml_files = list(directory.glob("*.xml"))
    if not xml_files:
        logging.warning(f"В директории {directory} не найдено файлов с расширением .xml.")
        return

    for xml_file in xml_files:
        process_xml_file(xml_file)

def main():
    logging.info("Начинается обработка...")
    
    for directory in DIRECTORIES:
        logging.info(f"Обработка директории: {directory}")
        
        # Обработка файла ExportPlaylist.cpls
        process_export_playlist(directory)
        
        # Переименование файлов .pcts в .xml
        rename_pcts_to_xml(directory)
        
        # Обработка всех XML файлов
        process_xml_files(directory)
    
    logging.info("Обработка завершена.")

if __name__ == "__main__":
    main()
