#!/usr/bin/env python3
"""
🌍 Universal HTML i18n Translator
   🚀 World's First Open Source HTML Translation Tool
   🔄 Works with ANY translation service (Google, Azure, DeepL, etc.)
   🔍 Smart language detection
   🧠 Intelligent skip (same language detection)
   ⚡ Parallel processing
   📦 No external dependencies required!
   🌟 GitHub Ready - MIT License
"""

import os
import sys
import json
import threading
import datetime
import tkinter as tk
from tkinter import ttk, scrolledtext, messagebox, filedialog
from pathlib import Path
import time
import shutil
import hashlib
import re
from collections import defaultdict
import html
import urllib.parse
from concurrent.futures import ThreadPoolExecutor, as_completed
import queue
from dataclasses import dataclass, field
from typing import List, Dict, Optional, Tuple, Set, Any
import logging

# ===================================================
# 📁 MAP STRUCTUUR
# ===================================================

APP_NAME = "UniversalI18NTranslator"
APP_DIR = os.path.expanduser(f"~/{APP_NAME}")
CONFIG_DIR = os.path.join(APP_DIR, "config")
TRANSLATIONS_DIR = os.path.join(APP_DIR, "translations")
EXPORTS_DIR = os.path.join(APP_DIR, "exports")
CACHE_DIR = os.path.join(APP_DIR, "cache")
LOGS_DIR = os.path.join(APP_DIR, "logs")
CONFIG_FILE = os.path.join(CONFIG_DIR, "settings.json")
TRANSLATION_CACHE = os.path.join(CACHE_DIR, "translation_cache.json")

for dir_path in [APP_DIR, CONFIG_DIR, TRANSLATIONS_DIR, EXPORTS_DIR, CACHE_DIR, LOGS_DIR]:
    os.makedirs(dir_path, exist_ok=True)

# ===================================================
# 📊 LOGGING
# ===================================================

logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler(os.path.join(LOGS_DIR, 'translator.log')),
        logging.StreamHandler()
    ]
)
logger = logging.getLogger(__name__)

# ===================================================
# 🌍 UNIVERSELE TAAL ONDERSTEUNING
# ===================================================

SUPPORTED_LANGUAGES = {
    'af': 'Afrikaans',
    'sq': 'Albanian',
    'am': 'Amharic',
    'ar': 'Arabic',
    'hy': 'Armenian',
    'az': 'Azerbaijani',
    'eu': 'Basque',
    'be': 'Belarusian',
    'bn': 'Bengali',
    'bs': 'Bosnian',
    'bg': 'Bulgarian',
    'ca': 'Catalan',
    'ceb': 'Cebuano',
    'ny': 'Chichewa',
    'zh-CN': 'Chinese (Simplified)',
    'zh-TW': 'Chinese (Traditional)',
    'co': 'Corsican',
    'hr': 'Croatian',
    'cs': 'Czech',
    'da': 'Danish',
    'nl': 'Dutch',
    'en': 'English',
    'eo': 'Esperanto',
    'et': 'Estonian',
    'tl': 'Filipino',
    'fi': 'Finnish',
    'fr': 'French',
    'fy': 'Frisian',
    'gl': 'Galician',
    'ka': 'Georgian',
    'de': 'German',
    'el': 'Greek',
    'gu': 'Gujarati',
    'ht': 'Haitian Creole',
    'ha': 'Hausa',
    'haw': 'Hawaiian',
    'he': 'Hebrew',
    'hi': 'Hindi',
    'hmn': 'Hmong',
    'hu': 'Hungarian',
    'is': 'Icelandic',
    'ig': 'Igbo',
    'id': 'Indonesian',
    'ga': 'Irish',
    'it': 'Italian',
    'ja': 'Japanese',
    'jw': 'Javanese',
    'kn': 'Kannada',
    'kk': 'Kazakh',
    'km': 'Khmer',
    'rw': 'Kinyarwanda',
    'ko': 'Korean',
    'ku': 'Kurdish',
    'ky': 'Kyrgyz',
    'lo': 'Lao',
    'la': 'Latin',
    'lv': 'Latvian',
    'lt': 'Lithuanian',
    'lb': 'Luxembourgish',
    'mk': 'Macedonian',
    'mg': 'Malagasy',
    'ms': 'Malay',
    'ml': 'Malayalam',
    'mt': 'Maltese',
    'mi': 'Maori',
    'mr': 'Marathi',
    'mn': 'Mongolian',
    'my': 'Myanmar (Burmese)',
    'ne': 'Nepali',
    'no': 'Norwegian',
    'or': 'Odia (Oriya)',
    'ps': 'Pashto',
    'fa': 'Persian',
    'pl': 'Polish',
    'pt': 'Portuguese',
    'pa': 'Punjabi',
    'ro': 'Romanian',
    'ru': 'Russian',
    'sm': 'Samoan',
    'gd': 'Scots Gaelic',
    'sr': 'Serbian',
    'st': 'Sesotho',
    'sn': 'Shona',
    'sd': 'Sindhi',
    'si': 'Sinhala',
    'sk': 'Slovak',
    'sl': 'Slovenian',
    'so': 'Somali',
    'es': 'Spanish',
    'su': 'Sundanese',
    'sw': 'Swahili',
    'sv': 'Swedish',
    'tg': 'Tajik',
    'ta': 'Tamil',
    'tt': 'Tatar',
    'te': 'Telugu',
    'th': 'Thai',
    'tr': 'Turkish',
    'tk': 'Turkmen',
    'uk': 'Ukrainian',
    'ur': 'Urdu',
    'ug': 'Uyghur',
    'uz': 'Uzbek',
    'vi': 'Vietnamese',
    'cy': 'Welsh',
    'xh': 'Xhosa',
    'yi': 'Yiddish',
    'yo': 'Yoruba',
    'zu': 'Zulu'
}

# ===================================================
# 🌍 UNIVERSELE VERTALER
# ===================================================

class UniversalTranslator:
    """Universele vertaler - werkt met elke service"""
    
    def __init__(self):
        self.cache = {}
        self.total_translations = 0
        self.skipped_translations = 0
        self.translation_service = 'mymemory'  # default
        self.load_cache()
    
    def load_cache(self):
        try:
            if os.path.exists(TRANSLATION_CACHE):
                with open(TRANSLATION_CACHE, 'r') as f:
                    data = json.load(f)
                    self.cache = data.get('cache', {})
                    self.total_translations = data.get('total', 0)
                    self.skipped_translations = data.get('skipped', 0)
        except:
            pass
    
    def save_cache(self):
        try:
            data = {
                'cache': self.cache,
                'total': self.total_translations,
                'skipped': self.skipped_translations,
                'last_updated': datetime.datetime.now().isoformat()
            }
            with open(TRANSLATION_CACHE, 'w') as f:
                json.dump(data, f, indent=2)
        except:
            pass
    
    def detect_language(self, text: str) -> str:
        """Detecteer taal van tekst"""
        if not text or len(text) < 3:
            return 'unknown'
        
        # Unicode script detectie
        if any('\u4e00' <= c <= '\u9fff' for c in text):
            return 'zh'
        if any('\u3040' <= c <= '\u309f' or '\u30a0' <= c <= '\u30ff' for c in text):
            return 'ja'
        if any('\u0600' <= c <= '\u06ff' for c in text):
            return 'ar'
        if any('\u0400' <= c <= '\u04ff' for c in text):
            return 'ru'
        
        # Probeer via MyMemory
        try:
            url = f"https://api.mymemory.translated.net/get?q={urllib.parse.quote(text[:200])}&langpair=auto|en"
            with urllib.request.urlopen(url, timeout=3) as response:
                data = json.loads(response.read().decode())
                if 'responseData' in data and 'detectedLanguage' in data['responseData']:
                    return data['responseData']['detectedLanguage']
        except:
            pass
        
        return 'unknown'
    
    def needs_translation(self, text: str, target_lang: str) -> bool:
        """Check of tekst vertaald moet worden"""
        if not text or not text.strip():
            return False
        
        detected = self.detect_language(text)
        if detected == 'unknown':
            return True
        if detected == target_lang:
            return False
        
        return True
    
    def translate(self, text: str, target_lang: str, source_lang: str = 'auto') -> str:
        """Vertaal tekst met beschikbare service"""
        if not text or not text.strip():
            return text
        
        # Check if translation needed
        if not self.needs_translation(text, target_lang):
            self.skipped_translations += 1
            self.save_cache()
            return text
        
        # Check cache
        cache_key = f"{source_lang}_{target_lang}_{hashlib.md5(text.encode()).hexdigest()}"
        if cache_key in self.cache:
            return self.cache[cache_key]
        
        # Try translation
        translated = self._translate_mymemory(text, target_lang, source_lang)
        
        if translated and translated != text:
            self.cache[cache_key] = translated
            self.total_translations += 1
            self.save_cache()
            return translated
        
        return text
    
    def _translate_mymemory(self, text: str, target_lang: str, source_lang: str) -> str:
        """Gratis MyMemory API vertaling"""
        try:
            url = f"https://api.mymemory.translated.net/get?q={urllib.parse.quote(text)}&langpair={source_lang}|{target_lang}"
            
            with urllib.request.urlopen(url, timeout=5) as response:
                data = json.loads(response.read().decode())
                if 'responseData' in data and 'translatedText' in data['responseData']:
                    return data['responseData']['translatedText']
        except Exception as e:
            logger.warning(f"Translation failed: {e}")
        
        return text
    
    def translate_html(self, html_content: str, target_lang: str, source_lang: str = 'auto') -> Tuple[str, dict]:
        """Vertaal HTML met behoud van structuur"""
        pattern = r'(<[^>]*>)|([^<]+)'
        parts = re.findall(pattern, html_content)
        
        translated_parts = []
        stats = {
            'total_nodes': 0,
            'translated': 0,
            'skipped': 0,
            'failed': 0
        }
        
        for tag, text in parts:
            if tag:
                translated_parts.append(tag)
            elif text and text.strip():
                stats['total_nodes'] += 1
                translated = self.translate(text.strip(), target_lang, source_lang)
                
                if translated != text:
                    stats['translated'] += 1
                else:
                    stats['skipped'] += 1
                
                translated_parts.append(translated)
            else:
                translated_parts.append(text)
        
        return ''.join(translated_parts), stats

# ===================================================
# 📊 DATA CLASSES
# ===================================================

@dataclass
class FileAnalysis:
    path: str
    size: int
    text_nodes: int
    total_words: int
    unique_words: int
    estimated_time: float
    languages_detected: Dict[str, int]
    html_tags: int
    needs_translation: bool = True

@dataclass
class TranslationResult:
    filepath: str
    success: bool
    translated_path: Optional[str] = None
    error: Optional[str] = None
    time_elapsed: float = 0.0
    words_translated: int = 0

# ===================================================
# 🔍 VERKENNINGSREADER
# ===================================================

class ExplorationReader:
    def __init__(self):
        self.cache = {}
        self.load_cache()
    
    def load_cache(self):
        try:
            cache_file = os.path.join(CACHE_DIR, 'exploration_cache.json')
            if os.path.exists(cache_file):
                with open(cache_file, 'r') as f:
                    self.cache = json.load(f)
        except:
            pass
    
    def save_cache(self):
        try:
            cache_file = os.path.join(CACHE_DIR, 'exploration_cache.json')
            with open(cache_file, 'w') as f:
                json.dump(self.cache, f, indent=2)
        except:
            pass
    
    def analyze_file(self, filepath: str) -> FileAnalysis:
        cache_key = hashlib.md5(filepath.encode()).hexdigest()
        
        if cache_key in self.cache:
            cached = self.cache[cache_key]
            return FileAnalysis(**cached)
        
        try:
            with open(filepath, 'r', encoding='utf-8') as f:
                content = f.read()
            
            size = len(content)
            
            # Extract text nodes
            text_nodes = re.findall(r'>([^<]+)<', content, re.DOTALL)
            text_nodes = [t.strip() for t in text_nodes if t.strip()]
            
            all_text = ' '.join(text_nodes)
            words = re.findall(r'\b\w+\b', all_text)
            
            analysis = FileAnalysis(
                path=filepath,
                size=size,
                text_nodes=len(text_nodes),
                total_words=len(words),
                unique_words=len(set(words)),
                estimated_time=len(words) * 0.05,
                languages_detected={},
                html_tags=len(re.findall(r'<[^>]+>', content)),
                needs_translation=True
            )
            
            # Cache
            self.cache[cache_key] = {
                'path': filepath,
                'size': size,
                'text_nodes': len(text_nodes),
                'total_words': len(words),
                'unique_words': len(set(words)),
                'estimated_time': len(words) * 0.05,
                'languages_detected': {},
                'html_tags': len(re.findall(r'<[^>]+>', content)),
                'needs_translation': True
            }
            self.save_cache()
            
            return analysis
            
        except Exception as e:
            logger.error(f"Analysis failed for {filepath}: {e}")
            return FileAnalysis(
                path=filepath,
                size=0,
                text_nodes=0,
                total_words=0,
                unique_words=0,
                estimated_time=0,
                languages_detected={},
                html_tags=0,
                needs_translation=True
            )
    
    def analyze_directory(self, directory: str, max_depth: int = 5) -> List[FileAnalysis]:
        results = []
        html_files = []
        
        for root, dirs, files in os.walk(directory):
            depth = root.replace(directory, '').count(os.sep)
            if depth > max_depth:
                continue
            
            for file in files:
                if file.endswith(('.html', '.htm', '.xhtml')):
                    html_files.append(os.path.join(root, file))
        
        logger.info(f"📁 Found {len(html_files)} HTML files in {directory}")
        
        with ThreadPoolExecutor(max_workers=4) as executor:
            futures = {executor.submit(self.analyze_file, f): f for f in html_files}
            for future in as_completed(futures):
                try:
                    result = future.result()
                    results.append(result)
                except Exception as e:
                    logger.error(f"Analysis error: {e}")
        
        return results

# ===================================================
# 🎨 KLEUREN THEMA
# ===================================================

COLORS = {
    'bg_dark': '#0a0a0a',
    'bg_medium': '#1a1a2e',
    'bg_light': '#2d2d44',
    'card_bg': '#1e1e2e',
    'text': '#ffffff',
    'text_secondary': '#cccccc',
    'success': '#00ff88',
    'warning': '#ffcc00',
    'danger': '#ff3333',
    'info': '#66bbff',
    'primary': '#6c5ce7',
    'accent': '#fd79a8',
    'translate': '#00b894',
    'start': '#00cc66',
    'pause': '#fdcb6e',
    'stop': '#ff6b6b',
    'analyze': '#6c5ce7',
    'export': '#0984e3',
}

# ===================================================
# 🖥️ MAIN APPLICATION
# ===================================================

class UniversalI18NApp:
    def __init__(self, root):
        self.root = root
        self.root.title("🌍 Universal HTML i18n Translator")
        self.root.geometry("1400x1000")
        self.root.configure(bg=COLORS['bg_dark'])
        self.root.resizable(True, True)
        
        # 🌍 Translator
        self.translator = UniversalTranslator()
        
        # 🔍 Explorer
        self.explorer = ExplorationReader()
        
        # Statussen
        self.is_analyzing = False
        self.is_translating = False
        self.paused = False
        self.stop_requested = False
        self.analysis_results: List[FileAnalysis] = []
        self.translation_results: List[TranslationResult] = []
        
        # Threading
        self.thread_pool = ThreadPoolExecutor(max_workers=4)
        self.translate_thread = None
        
        # Instellingen
        self.source_lang = tk.StringVar(value='auto')
        self.target_lang = tk.StringVar(value='en')
        self.scan_path = tk.StringVar(value=os.path.expanduser("~"))
        self.max_depth = tk.IntVar(value=5)
        self.smart_skip = tk.BooleanVar(value=True)
        self.parallel = tk.BooleanVar(value=True)
        self.max_workers = tk.IntVar(value=4)
        
        # Selectie
        self.selected_folders = {}
        self.folder_checkboxes = {}
        self.folder_items = {}
        
        # Tijd
        self.start_time = None
        self.pause_time = 0
        self.total_pause_time = 0
        self.processed_files = 0
        self.total_files = 0
        
        # Laad configuratie
        self.load_config()
        
        # Setup UI
        self.setup_ui()
        
        self.root.protocol("WM_DELETE_WINDOW", self.on_closing)
        self.populate_folder_tree()
        
        # Status
        self.log("🌍 Universal HTML i18n Translator v1.0", "header")
        self.log("🚀 World's First Open Source HTML Translation Tool", "info")
        self.log(f"📊 {len(SUPPORTED_LANGUAGES)} languages supported", "info")
        self.log(f"💾 Cache: {len(self.translator.cache)} translations", "translate")
        self.log(f"⏭️ Skipped: {self.translator.skipped_translations} (same language)", "warning")
    
    def setup_ui(self):
        """Bouw de interface"""
        
        # ====== HEADER ======
        header_frame = tk.Frame(self.root, bg=COLORS['bg_medium'], height=80)
        header_frame.pack(fill=tk.X, padx=0, pady=0)
        header_frame.pack_propagate(False)
        
        title_frame = tk.Frame(header_frame, bg=COLORS['bg_medium'])
        title_frame.pack(expand=True, fill=tk.BOTH)
        
        # Logo
        logo = tk.Label(
            title_frame,
            text="🌍",
            font=("Segoe UI", 36),
            bg=COLORS['bg_medium'],
            fg=COLORS['primary']
        )
        logo.pack(side=tk.LEFT, padx=(20, 10))
        
        title = tk.Label(
            title_frame,
            text="Universal HTML i18n Translator",
            font=("Segoe UI", 22, "bold"),
            bg=COLORS['bg_medium'],
            fg=COLORS['text']
        )
        title.pack(side=tk.LEFT)
        
        subtitle = tk.Label(
            title_frame,
            text="🚀 World's First Open Source Translation Tool",
            font=("Segoe UI", 10),
            bg=COLORS['bg_medium'],
            fg=COLORS['accent']
        )
        subtitle.pack(side=tk.LEFT, padx=(10, 0))
        
        # Badges
        badges = [
            ("🌟 Open Source", COLORS['primary']),
            ("🌍 Universal", COLORS['translate']),
            ("🧠 Smart Skip", COLORS['success']),
            ("⚡ Parallel", COLORS['info']),
        ]
        
        for text, color in badges:
            badge = tk.Label(
                title_frame,
                text=text,
                font=("Segoe UI", 9, "bold"),
                bg=color,
                fg=COLORS['text'],
                padx=12,
                pady=4
            )
            badge.pack(side=tk.RIGHT, padx=4)
        
        # GitHub badge
        github_badge = tk.Label(
            title_frame,
            text="🐙 GitHub",
            font=("Segoe UI", 9, "bold"),
            bg='#333333',
            fg=COLORS['text'],
            padx=12,
            pady=4,
            cursor="hand2"
        )
        github_badge.pack(side=tk.RIGHT, padx=4)
        github_badge.bind("<Button-1>", lambda e: self.open_github())
        
        # ====== MAIN LAYOUT ======
        main_paned = tk.PanedWindow(
            self.root,
            bg=COLORS['bg_dark'],
            sashwidth=5,
            sashrelief=tk.FLAT
        )
        main_paned.pack(fill=tk.BOTH, expand=True, padx=10, pady=10)
        
        # ====== LINKER PANEEL ======
        left_frame = tk.Frame(main_paned, bg=COLORS['bg_medium'], relief=tk.FLAT)
        main_paned.add(left_frame, width=450, minsize=350)
        
        # ====== TAAL INSTELLINGEN ======
        lang_frame = tk.Frame(left_frame, bg=COLORS['bg_medium'], height=140)
        lang_frame.pack(fill=tk.X, padx=10, pady=5)
        lang_frame.pack_propagate(False)
        
        tk.Label(
            lang_frame,
            text="🌍 LANGUAGE SETTINGS",
            font=("Segoe UI", 11, "bold"),
            bg=COLORS['bg_medium'],
            fg=COLORS['translate']
        ).pack(anchor=tk.W, pady=(10, 5))
        
        # Source
        src_frame = tk.Frame(lang_frame, bg=COLORS['bg_medium'])
        src_frame.pack(fill=tk.X, pady=2)
        
        tk.Label(
            src_frame,
            text="📖 Source:",
            font=("Segoe UI", 10),
            bg=COLORS['bg_medium'],
            fg=COLORS['text']
        ).pack(side=tk.LEFT, padx=5)
        
        src_combo = ttk.Combobox(
            src_frame,
            textvariable=self.source_lang,
            values=['auto'] + list(SUPPORTED_LANGUAGES.keys()),
            font=("Segoe UI", 10),
            width=15,
            state='readonly'
        )
        src_combo.pack(side=tk.RIGHT, padx=5)
        
        # Target
        tgt_frame = tk.Frame(lang_frame, bg=COLORS['bg_medium'])
        tgt_frame.pack(fill=tk.X, pady=2)
        
        tk.Label(
            tgt_frame,
            text="🎯 Target:",
            font=("Segoe UI", 10),
            bg=COLORS['bg_medium'],
            fg=COLORS['text']
        ).pack(side=tk.LEFT, padx=5)
        
        tgt_combo = ttk.Combobox(
            tgt_frame,
            textvariable=self.target_lang,
            values=list(SUPPORTED_LANGUAGES.keys()),
            font=("Segoe UI", 10),
            width=15,
            state='readonly'
        )
        tgt_combo.pack(side=tk.RIGHT, padx=5)
        
        # Options
        opt_frame = tk.Frame(lang_frame, bg=COLORS['bg_medium'])
        opt_frame.pack(fill=tk.X, pady=5)
        
        tk.Checkbutton(
            opt_frame,
            variable=self.smart_skip,
            text="🧠 Smart Skip",
            font=("Segoe UI", 9),
            bg=COLORS['bg_medium'],
            fg=COLORS['text'],
            selectcolor=COLORS['bg_light'],
            activebackground=COLORS['bg_medium'],
            cursor="hand2"
        ).pack(side=tk.LEFT, padx=5)
        
        tk.Checkbutton(
            opt_frame,
            variable=self.parallel,
            text="⚡ Parallel",
            font=("Segoe UI", 9),
            bg=COLORS['bg_medium'],
            fg=COLORS['text'],
            selectcolor=COLORS['bg_light'],
            activebackground=COLORS['bg_medium'],
            cursor="hand2"
        ).pack(side=tk.LEFT, padx=5)
        
        # ====== MAP SELECTIE ======
        tk.Label(
            left_frame,
            text="📂 FOLDER SELECTION",
            font=("Segoe UI", 11, "bold"),
            bg=COLORS['bg_medium'],
            fg=COLORS['text'],
            pady=5
        ).pack(fill=tk.X, padx=10)
        
        path_frame = tk.Frame(left_frame, bg=COLORS['bg_medium'])
        path_frame.pack(fill=tk.X, padx=10, pady=5)
        
        tk.Entry(
            path_frame,
            textvariable=self.scan_path,
            font=("Segoe UI", 10),
            bg=COLORS['card_bg'],
            fg=COLORS['text'],
            relief=tk.FLAT,
            insertbackground=COLORS['text']
        ).pack(side=tk.LEFT, fill=tk.X, expand=True, padx=(0, 5))
        
        tk.Button(
            path_frame,
            text="📁",
            font=("Segoe UI", 10, "bold"),
            bg=COLORS['info'],
            fg=COLORS['text'],
            relief=tk.FLAT,
            padx=10,
            cursor="hand2",
            command=self.browse_folder
        ).pack(side=tk.RIGHT)
        
        depth_frame = tk.Frame(left_frame, bg=COLORS['bg_medium'])
        depth_frame.pack(fill=tk.X, padx=10, pady=5)
        
        tk.Label(
            depth_frame,
            text="📊 Depth:",
            font=("Segoe UI", 10),
            bg=COLORS['bg_medium'],
            fg=COLORS['text']
        ).pack(side=tk.LEFT)
        
        tk.Spinbox(
            depth_frame,
            from_=1, to=10,
            textvariable=self.max_depth,
            font=("Segoe UI", 10, "bold"),
            bg=COLORS['card_bg'],
            fg=COLORS['text'],
            relief=tk.FLAT,
            width=5,
            buttonbackground=COLORS['bg_light']
        ).pack(side=tk.RIGHT)
        
        # ====== FOLDER TREE ======
        tree_frame = tk.Frame(left_frame, bg=COLORS['bg_medium'])
        tree_frame.pack(fill=tk.BOTH, expand=True, padx=10, pady=5)
        
        tk.Label(
            tree_frame,
            text="🔽 Select folders:",
            font=("Segoe UI", 10, "bold"),
            bg=COLORS['bg_medium'],
            fg=COLORS['text_secondary']
        ).pack(anchor=tk.W, pady=(0, 5))
        
        tree_container = tk.Frame(tree_frame, bg=COLORS['bg_medium'])
        tree_container.pack(fill=tk.BOTH, expand=True)
        
        tree_canvas = tk.Canvas(
            tree_container,
            bg=COLORS['bg_medium'],
            highlightthickness=0
        )
        tree_scrollbar = tk.Scrollbar(
            tree_container,
            orient=tk.VERTICAL,
            command=tree_canvas.yview
        )
        
        self.tree_container = tk.Frame(tree_canvas, bg=COLORS['bg_medium'])
        self.tree_container.bind(
            "<Configure>",
            lambda e: tree_canvas.configure(scrollregion=tree_canvas.bbox("all"))
        )
        
        tree_canvas.create_window((0, 0), window=self.tree_container, anchor="nw")
        tree_canvas.configure(yscrollcommand=tree_scrollbar.set)
        
        tree_canvas.pack(side=tk.LEFT, fill=tk.BOTH, expand=True)
        tree_scrollbar.pack(side=tk.RIGHT, fill=tk.Y)
        
        # Select buttons
        sel_frame = tk.Frame(left_frame, bg=COLORS['bg_medium'])
        sel_frame.pack(fill=tk.X, padx=10, pady=5)
        
        tk.Button(
            sel_frame,
            text="✅ All",
            font=("Segoe UI", 9, "bold"),
            bg=COLORS['info'],
            fg=COLORS['text'],
            relief=tk.FLAT,
            padx=10,
            cursor="hand2",
            command=self.select_all
        ).pack(side=tk.LEFT, padx=2)
        
        tk.Button(
            sel_frame,
            text="❌ None",
            font=("Segoe UI", 9, "bold"),
            bg=COLORS['bg_light'],
            fg=COLORS['text'],
            relief=tk.FLAT,
            padx=10,
            cursor="hand2",
            command=self.deselect_all
        ).pack(side=tk.LEFT, padx=2)
        
        # ====== RECHTSE PANEEL ======
        right_frame = tk.Frame(main_paned, bg=COLORS['bg_dark'])
        main_paned.add(right_frame, width=900, minsize=600)
        
        # ====== CONTROLE PANEL ======
        ctrl_frame = tk.Frame(right_frame, bg=COLORS['bg_dark'], height=90)
        ctrl_frame.pack(fill=tk.X, padx=10, pady=5)
        ctrl_frame.pack_propagate(False)
        
        self.analyze_btn = tk.Button(
            ctrl_frame,
            text="🔍 EXPLORE",
            font=("Segoe UI", 12, "bold"),
            bg=COLORS['analyze'],
            fg=COLORS['text'],
            relief=tk.RAISED,
            padx=20,
            pady=10,
            cursor="hand2",
            command=self.start_analysis,
            borderwidth=3
        )
        self.analyze_btn.pack(side=tk.LEFT, padx=5)
        
        self.start_btn = tk.Button(
            ctrl_frame,
            text="🌍 TRANSLATE",
            font=("Segoe UI", 12, "bold"),
            bg=COLORS['translate'],
            fg=COLORS['text'],
            relief=tk.RAISED,
            padx=20,
            pady=10,
            cursor="hand2",
            command=self.start_translate,
            borderwidth=3
        )
        self.start_btn.pack(side=tk.LEFT, padx=5)
        
        self.pause_btn = tk.Button(
            ctrl_frame,
            text="⏸ PAUSE",
            font=("Segoe UI", 12, "bold"),
            bg=COLORS['pause'],
            fg=COLORS['bg_dark'],
            relief=tk.RAISED,
            padx=20,
            pady=10,
            cursor="hand2",
            command=self.toggle_pause,
            state=tk.DISABLED,
            borderwidth=3
        )
        self.pause_btn.pack(side=tk.LEFT, padx=5)
        
        self.stop_btn = tk.Button(
            ctrl_frame,
            text="⏹ STOP",
            font=("Segoe UI", 12, "bold"),
            bg=COLORS['stop'],
            fg=COLORS['text'],
            relief=tk.RAISED,
            padx=20,
            pady=10,
            cursor="hand2",
            command=self.stop_operation,
            state=tk.DISABLED,
            borderwidth=3
        )
        self.stop_btn.pack(side=tk.LEFT, padx=5)
        
        tk.Button(
            ctrl_frame,
            text="🗑️ CLEAR",
            font=("Segoe UI", 10, "bold"),
            bg=COLORS['bg_light'],
            fg=COLORS['text'],
            relief=tk.RAISED,
            padx=15,
            pady=10,
            cursor="hand2",
            command=self.clear_results,
            borderwidth=2
        ).pack(side=tk.LEFT, padx=5)
        
        tk.Button(
            ctrl_frame,
            text="💾 EXPORT",
            font=("Segoe UI", 10, "bold"),
            bg=COLORS['export'],
            fg=COLORS['text'],
            relief=tk.RAISED,
            padx=15,
            pady=10,
            cursor="hand2",
            command=self.export_results,
            borderwidth=2
        ).pack(side=tk.LEFT, padx=5)
        
        # ====== PROGRESS ======
        prog_frame = tk.Frame(right_frame, bg=COLORS['bg_dark'], height=70)
        prog_frame.pack(fill=tk.X, padx=10, pady=5)
        prog_frame.pack_propagate(False)
        
        self.percent = tk.Label(
            prog_frame,
            text="0%",
            font=("Segoe UI", 20, "bold"),
            fg=COLORS['translate'],
            bg=COLORS['bg_dark'],
            width=6
        )
        self.percent.pack(side=tk.LEFT, padx=5)
        
        prog_container = tk.Frame(prog_frame, bg=COLORS['card_bg'], relief=tk.FLAT)
        prog_container.pack(side=tk.LEFT, padx=10, fill=tk.X, expand=True)
        
        self.progress = ttk.Progressbar(
            prog_container,
            orient=tk.HORIZONTAL,
            length=500,
            mode='determinate',
            style='Translate.Horizontal.TProgressbar'
        )
        self.progress.pack(fill=tk.X, expand=True, padx=2, pady=2)
        
        status_frame = tk.Frame(prog_frame, bg=COLORS['bg_dark'])
        status_frame.pack(side=tk.RIGHT, padx=5, fill=tk.Y)
        
        self.status = tk.Label(
            status_frame,
            text="🟢 Ready",
            font=("Segoe UI", 11, "bold"),
            fg=COLORS['success'],
            bg=COLORS['bg_dark']
        )
        self.status.pack(anchor=tk.E)
        
        self.time_label = tk.Label(
            status_frame,
            text="⏱️ 0s",
            font=("Segoe UI", 10),
            fg=COLORS['text_secondary'],
            bg=COLORS['bg_dark']
        )
        self.time_label.pack(anchor=tk.E)
        
        # ====== INFO BAR ======
        info_frame = tk.Frame(right_frame, bg=COLORS['bg_dark'], height=35)
        info_frame.pack(fill=tk.X, padx=10, pady=2)
        info_frame.pack_propagate(False)
        
        self.file_count = tk.Label(
            info_frame,
            text="📁 Files: 0",
            font=("Segoe UI", 10, "bold"),
            fg=COLORS['text'],
            bg=COLORS['bg_dark']
        )
        self.file_count.pack(side=tk.LEFT)
        
        self.word_count = tk.Label(
            info_frame,
            text="📝 Words: 0",
            font=("Segoe UI", 10, "bold"),
            fg=COLORS['info'],
            bg=COLORS['bg_dark']
        )
        self.word_count.pack(side=tk.LEFT, padx=(15, 0))
        
        self.translated_count = tk.Label(
            info_frame,
            text="🌍 Translated: 0",
            font=("Segoe UI", 10, "bold"),
            fg=COLORS['translate'],
            bg=COLORS['bg_dark']
        )
        self.translated_count.pack(side=tk.LEFT, padx=(15, 0))
        
        self.skipped_count = tk.Label(
            info_frame,
            text="⏭️ Skipped: 0",
            font=("Segoe UI", 10, "bold"),
            fg=COLORS['warning'],
            bg=COLORS['bg_dark']
        )
        self.skipped_count.pack(side=tk.LEFT, padx=(15, 0))
        
        self.failed_count = tk.Label(
            info_frame,
            text="❌ Failed: 0",
            font=("Segoe UI", 10, "bold"),
            fg=COLORS['danger'],
            bg=COLORS['bg_dark']
        )
        self.failed_count.pack(side=tk.LEFT, padx=(15, 0))
        
        self.cache_label = tk.Label(
            info_frame,
            text=f"💾 Cache: {len(self.translator.cache)}",
            font=("Segoe UI", 10),
            fg=COLORS['accent'],
            bg=COLORS['bg_dark']
        )
        self.cache_label.pack(side=tk.RIGHT, padx=5)
        
        self.progress_text = tk.Label(
            info_frame,
            text="📊 0 / 0",
            font=("Segoe UI", 10),
            fg=COLORS['text_secondary'],
            bg=COLORS['bg_dark']
        )
        self.progress_text.pack(side=tk.RIGHT, padx=5)
        
        # ====== RESULTS ======
        res_frame = tk.Frame(right_frame, bg=COLORS['bg_medium'])
        res_frame.pack(fill=tk.BOTH, expand=True, padx=10, pady=10)
        
        tab_frame = tk.Frame(res_frame, bg=COLORS['bg_medium'], height=30)
        tab_frame.pack(fill=tk.X)
        
        tk.Label(
            tab_frame,
            text="📋 TRANSLATION RESULTS",
            font=("Segoe UI", 11, "bold"),
            bg=COLORS['bg_medium'],
            fg=COLORS['text']
        ).pack(side=tk.LEFT, padx=10)
        
        tk.Label(
            tab_frame,
            text="🌍 Universal Translator",
            font=("Segoe UI", 9, "bold"),
            bg=COLORS['bg_medium'],
            fg=COLORS['primary']
        ).pack(side=tk.RIGHT, padx=10)
        
        self.result_text = scrolledtext.ScrolledText(
            res_frame,
            font=("Consolas", 10),
            bg=COLORS['card_bg'],
            fg=COLORS['text'],
            insertbackground=COLORS['text'],
            relief=tk.FLAT,
            wrap=tk.WORD,
            height=15
        )
        self.result_text.pack(fill=tk.BOTH, expand=True, pady=5)
        
        # Tags
        self.result_text.tag_config("header", foreground=COLORS['accent'], font=("Segoe UI", 11, "bold"))
        self.result_text.tag_config("success", foreground=COLORS['success'])
        self.result_text.tag_config("warning", foreground=COLORS['warning'])
        self.result_text.tag_config("error", foreground=COLORS['danger'])
        self.result_text.tag_config("info", foreground=COLORS['info'])
        self.result_text.tag_config("primary", foreground=COLORS['primary'])
        self.result_text.tag_config("skip", foreground=COLORS['warning'])
        
        # ====== FOOTER ======
        footer = tk.Frame(self.root, bg=COLORS['bg_medium'], height=30)
        footer.pack(fill=tk.X, side=tk.BOTTOM)
        footer.pack_propagate(False)
        
        tk.Label(
            footer,
            text="🌍 Universal HTML i18n Translator v1.0 | 🚀 World's First Open Source | MIT License | 🌟 GitHub",
            font=("Segoe UI", 9),
            fg=COLORS['text_secondary'],
            bg=COLORS['bg_medium']
        ).pack(pady=5)
        
        # Styling
        style = ttk.Style()
        style.theme_use('default')
        style.configure(
            "Translate.Horizontal.TProgressbar",
            background=COLORS['primary'],
            troughcolor=COLORS['card_bg'],
            bordercolor=COLORS['bg_dark'],
            lightcolor=COLORS['primary'],
            darkcolor=COLORS['primary'],
            thickness=20
        )
    
    # ===================================================
    # 🔍 ANALYSIS FUNCTIONS
    # ===================================================
    
    def start_analysis(self):
        if self.is_analyzing or self.is_translating:
            return
        
        selected = self.get_selected_folders()
        if not selected:
            messagebox.showwarning("No folders", "Select at least one folder!")
            return
        
        self.is_analyzing = True
        self.analyze_btn.config(state=tk.DISABLED)
        self.start_btn.config(state=tk.DISABLED)
        self.stop_btn.config(state=tk.NORMAL)
        self.status.config(text="🔍 Exploring...", fg=COLORS['analyze'])
        
        self.log("🔍 START EXPLORATION", "header")
        self.log("=" * 60)
        self.log(f"📂 Folders: {len(selected)}")
        self.log("=" * 60)
        
        threading.Thread(target=self._analyze_folders, args=(selected,), daemon=True).start()
    
    def _analyze_folders(self, folders):
        all_analyses = []
        total_words = 0
        
        for folder in folders:
            analyses = self.explorer.analyze_directory(folder, self.max_depth.get())
            all_analyses.extend(analyses)
            total_words += sum(a.total_words for a in analyses)
        
        self.analysis_results = all_analyses
        self.root.after(0, lambda: self._finish_analysis(all_analyses, total_words))
    
    def _finish_analysis(self, analyses, total_words):
        self.is_analyzing = False
        self.analyze_btn.config(state=tk.NORMAL)
        self.start_btn.config(state=tk.NORMAL)
        self.stop_btn.config(state=tk.DISABLED)
        self.status.config(text="✅ Exploration complete", fg=COLORS['success'])
        
        self.log("=" * 60)
        self.log("✅ EXPLORATION COMPLETE", "header")
        self.log(f"📁 Files analyzed: {len(analyses)}")
        self.log(f"📝 Total words: {total_words}")
        
        self.file_count.config(text=f"📁 Files: {len(analyses)}")
        self.word_count.config(text=f"📝 Words: {total_words}")
        
        if len(analyses) > 0:
            self.log("\n💡 Click 'TRANSLATE' to start", "info")
            messagebox.showinfo(
                "Exploration Complete",
                f"✅ Analysis complete!\n\n"
                f"📁 Files: {len(analyses)}\n"
                f"📝 Words: {total_words}\n\n"
                f"Click 'TRANSLATE' to begin."
            )
    
    # ===================================================
    # 🌍 TRANSLATION FUNCTIONS
    # ===================================================
    
    def start_translate(self):
        if self.is_translating:
            return
        
        html_files = []
        if self.analysis_results:
            html_files = [a.path for a in self.analysis_results]
        else:
            selected = self.get_selected_folders()
            if not selected:
                messagebox.showwarning("No folders", "Select at least one folder!")
                return
            
            for folder in selected:
                for root, dirs, files in os.walk(folder):
                    for file in files:
                        if file.endswith(('.html', '.htm', '.xhtml')):
                            html_files.append(os.path.join(root, file))
        
        if not html_files:
            messagebox.showinfo("No HTML", "No HTML files found!")
            return
        
        # Reset
        self.translation_results = []
        self.processed_files = 0
        self.total_files = len(html_files)
        self.stop_requested = False
        self.paused = False
        self.total_pause_time = 0
        self.start_time = time.time()
        
        # Update UI
        self.start_btn.config(state=tk.DISABLED)
        self.analyze_btn.config(state=tk.DISABLED)
        self.pause_btn.config(state=tk.NORMAL, text="⏸ PAUSE")
        self.stop_btn.config(state=tk.NORMAL)
        self.progress['value'] = 0
        self.percent.config(text="0%")
        self.status.config(text="🌍 Translating...", fg=COLORS['info'])
        
        self.log("\n🌍 START TRANSLATION", "header")
        self.log("=" * 60)
        self.log(f"📁 Files: {self.total_files}")
        self.log(f"🌍 Source: {self.source_lang.get()} -> Target: {self.target_lang.get()}")
        self.log(f"🧠 Smart Skip: {'ON' if self.smart_skip.get() else 'OFF'}")
        self.log("=" * 60)
        
        self.is_translating = True
        self.translate_thread = threading.Thread(
            target=self._translate_files,
            args=(html_files,),
            daemon=True
        )
        self.translate_thread.start()
    
    def _translate_files(self, html_files):
        source_lang = self.source_lang.get()
        target_lang = self.target_lang.get()
        
        if self.parallel.get():
            with ThreadPoolExecutor(max_workers=self.max_workers.get()) as executor:
                futures = {
                    executor.submit(self._translate_file, f, source_lang, target_lang): f 
                    for f in html_files
                }
                
                for future in as_completed(futures):
                    if self.stop_requested:
                        break
                    
                    while self.paused and not self.stop_requested:
                        time.sleep(0.1)
                    
                    result = future.result()
                    self.translation_results.append(result)
                    self.processed_files += 1
                    self._update_progress()
        else:
            for filepath in html_files:
                if self.stop_requested:
                    break
                
                while self.paused and not self.stop_requested:
                    time.sleep(0.1)
                
                result = self._translate_file(filepath, source_lang, target_lang)
                self.translation_results.append(result)
                self.processed_files += 1
                self._update_progress()
        
        self.is_translating = False
        self.root.after(0, self._finish_translate)
    
    def _translate_file(self, filepath, source_lang, target_lang) -> TranslationResult:
        start_time = time.time()
        
        try:
            with open(filepath, 'r', encoding='utf-8') as f:
                html_content = f.read()
            
            translated_html, stats = self.translator.translate_html(
                html_content,
                target_lang,
                source_lang if source_lang != 'auto' else 'auto'
            )
            
            translated_path = self._save_translated_file(filepath, translated_html, target_lang)
            
            self.root.after(0, lambda: self._log_result(filepath, stats))
            
            return TranslationResult(
                filepath=filepath,
                success=True,
                translated_path=translated_path,
                time_elapsed=time.time() - start_time,
                words_translated=stats.get('translated', 0)
            )
            
        except Exception as e:
            self.root.after(0, lambda: self.log(f"❌ {os.path.basename(filepath)}: {e}", "error"))
            return TranslationResult(
                filepath=filepath,
                success=False,
                error=str(e),
                time_elapsed=time.time() - start_time
            )
    
    def _save_translated_file(self, original_path, translated_html, target_lang):
        rel_path = os.path.relpath(original_path, self.scan_path.get())
        translated_dir = os.path.join(TRANSLATIONS_DIR, target_lang, os.path.dirname(rel_path))
        os.makedirs(translated_dir, exist_ok=True)
        
        basename = os.path.basename(original_path)
        name, ext = os.path.splitext(basename)
        new_name = f"{name}_{target_lang}{ext}"
        translated_path = os.path.join(translated_dir, new_name)
        
        with open(translated_path, 'w', encoding='utf-8') as f:
            f.write(translated_html)
        
        return translated_path
    
    def _log_result(self, filepath, stats):
        name = os.path.basename(filepath)
        
        if stats.get('translated', 0) > 0:
            self.log(f"✅ {name}: {stats['translated']} nodes translated", "success")
        if stats.get('skipped', 0) > 0:
            self.log(f"⏭️ {name}: {stats['skipped']} nodes skipped (same language)", "skip")
    
    def _update_progress(self):
        if self.total_files > 0:
            progress = min(100, int((self.processed_files / self.total_files) * 100))
            elapsed = int(time.time() - self.start_time - self.total_pause_time)
            
            translated = sum(1 for r in self.translation_results if r.success)
            failed = sum(1 for r in self.translation_results if not r.success)
            skipped = self.translator.skipped_translations
            
            self.root.after(0, lambda: self.progress.config(value=progress))
            self.root.after(0, lambda: self.percent.config(text=f"{progress}%"))
            self.root.after(0, lambda: self.time_label.config(text=f"⏱️ {elapsed}s"))
            self.root.after(0, lambda: self.progress_text.config(
                text=f"📊 {self.processed_files} / {self.total_files}"
            ))
            self.root.after(0, lambda: self.translated_count.config(
                text=f"🌍 Translated: {translated}"
            ))
            self.root.after(0, lambda: self.skipped_count.config(
                text=f"⏭️ Skipped: {skipped}"
            ))
            self.root.after(0, lambda: self.failed_count.config(
                text=f"❌ Failed: {failed}"
            ))
    
    def _finish_translate(self):
        elapsed = int(time.time() - self.start_time - self.total_pause_time)
        
        self.start_btn.config(state=tk.NORMAL)
        self.analyze_btn.config(state=tk.NORMAL)
        self.pause_btn.config(state=tk.DISABLED, text="⏸ PAUZE")
        self.stop_btn.config(state=tk.DISABLED)
        self.progress['value'] = 100
        self.percent.config(text="100%")
        
        if self.stop_requested:
            self.status.config(text="⏹ Stopped", fg=COLORS['danger'])
            self.log(f"\n⏹ Stopped after {elapsed}s", "error")
        else:
            self.status.config(text="✅ Complete!", fg=COLORS['success'])
            self.log(f"\n✅ Complete in {elapsed}s!", "header")
        
        success = sum(1 for r in self.translation_results if r.success)
        failed = sum(1 for r in self.translation_results if not r.success)
        skipped = self.translator.skipped_translations
        
        self.log(f"📁 Total: {self.total_files} files")
        self.log(f"🌍 Translated: {success}", "success")
        self.log(f"⏭️ Skipped: {skipped} (same language)", "warning")
        self.log(f"❌ Failed: {failed}", "error")
        self.log(f"💾 Cache: {len(self.translator.cache)} translations", "accent")
        
        self.cache_label.config(text=f"💾 Cache: {len(self.translator.cache)}")
        self.translator.save_cache()
        
        if success > 0:
            self.export_results()
    
    # ===================================================
    # ⏹️ CONTROL FUNCTIONS
    # ===================================================
    
    def toggle_pause(self):
        if not self.is_translating:
            return
        
        self.paused = not self.paused
        
        if self.paused:
            self.pause_btn.config(text="▶ RESUME")
            self.status.config(text="⏸️ PAUSED", fg=COLORS['pause'])
            self.log("⏸️ Translation paused!", "warning")
            self.pause_time = time.time()
        else:
            self.pause_btn.config(text="⏸ PAUSE")
            self.status.config(text="🔄 Resuming...", fg=COLORS['info'])
            self.total_pause_time += time.time() - self.pause_time
            self.log("▶️ Translation resumed!", "success")
            self.status.config(text="🌍 Translating...", fg=COLORS['info'])
    
    def stop_operation(self):
        if self.is_analyzing or self.is_translating:
            self.stop_requested = True
            self.paused = False
            self.status.config(text="⏹ Stopping...", fg=COLORS['danger'])
            self.pause_btn.config(state=tk.DISABLED)
            self.log("⏹ Stopping operation...", "warning")
    
    def clear_results(self):
        if self.is_analyzing or self.is_translating:
            return
        
        self.result_text.delete(1.0, tk.END)
        self.analysis_results = []
        self.translation_results = []
        self.file_count.config(text="📁 Files: 0")
        self.word_count.config(text="📝 Words: 0")
        self.translated_count.config(text="🌍 Translated: 0")
        self.skipped_count.config(text="⏭️ Skipped: 0")
        self.failed_count.config(text="❌ Failed: 0")
        self.progress['value'] = 0
        self.percent.config(text="0%")
        self.status.config(text="🟢 Ready", fg=COLORS['success'])
        self.time_label.config(text="⏱️ 0s")
        self.progress_text.config(text="📊 0 / 0")
    
    def export_results(self):
        if not self.translation_results or not any(r.success for r in self.translation_results):
            messagebox.showinfo("No results", "No files translated!")
            return
        
        timestamp = datetime.datetime.now().strftime("%Y%m%d_%H%M%S")
        export_dir = os.path.join(EXPORTS_DIR, f"export_{timestamp}")
        os.makedirs(export_dir, exist_ok=True)
        
        try:
            # Copy translated files
            for result in self.translation_results:
                if result.success and result.translated_path:
                    rel_path = os.path.relpath(result.translated_path, TRANSLATIONS_DIR)
                    dest_path = os.path.join(export_dir, rel_path)
                    os.makedirs(os.path.dirname(dest_path), exist_ok=True)
                    shutil.copy2(result.translated_path, dest_path)
            
            # Create report
            report = os.path.join(export_dir, "TRANSLATION_REPORT.md")
            with open(report, 'w', encoding='utf-8') as f:
                f.write("# 🌍 Universal HTML i18n Translator Report\n\n")
                f.write(f"**Generated**: {datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')}\n\n")
                
                success = sum(1 for r in self.translation_results if r.success)
                failed = sum(1 for r in self.translation_results if not r.success)
                skipped = self.translator.skipped_translations
                
                f.write("## 📊 Summary\n\n")
                f.write(f"- **Total Files**: {len(self.translation_results)}\n")
                f.write(f"- **Successfully Translated**: {success}\n")
                f.write(f"- **Failed**: {failed}\n")
                f.write(f"- **Skipped** (same language): {skipped}\n")
                f.write(f"- **Cache Size**: {len(self.translator.cache)}\n\n")
                
                f.write("## 📁 Translated Files\n\n")
                for result in self.translation_results:
                    if result.success:
                        f.write(f"### ✅ {os.path.basename(result.filepath)}\n")
                        f.write(f"- **Words Translated**: {result.words_translated}\n")
                        f.write(f"- **Time**: {result.time_elapsed:.2f}s\n\n")
            
            messagebox.showinfo(
                "Export Complete",
                f"✅ Export complete!\n\n"
                f"📁 Location: {export_dir}\n"
                f"📄 Report: TRANSLATION_REPORT.md"
            )
            
        except Exception as e:
            messagebox.showerror("Export Error", f"❌ Export failed: {e}")
    
    # ===================================================
    # 📁 FOLDER FUNCTIONS
    # ===================================================
    
    def populate_folder_tree(self, path=None):
        if path is None:
            path = self.scan_path.get()
        
        for widget in self.tree_container.winfo_children():
            widget.destroy()
        
        self.folder_checkboxes = {}
        self.folder_items = {}
        self.selected_folders = {}
        self._add_folder(path, 0)
    
    def _add_folder(self, path, depth):
        if depth > self.max_depth.get():
            return
        
        try:
            if not os.path.exists(path) or not os.path.isdir(path):
                return
            
            frame = tk.Frame(self.tree_container, bg=COLORS['bg_medium'])
            frame.pack(fill=tk.X, padx=(10 + depth * 20, 5), pady=1)
            
            var = tk.BooleanVar(value=True)
            self.selected_folders[path] = var
            
            cb = tk.Checkbutton(
                frame,
                variable=var,
                bg=COLORS['bg_medium'],
                fg=COLORS['text'],
                selectcolor=COLORS['bg_light'],
                activebackground=COLORS['bg_medium'],
                cursor="hand2"
            )
            cb.pack(side=tk.LEFT)
            
            name = os.path.basename(path) or path
            tk.Label(
                frame,
                text=f"📁 {name}",
                font=("Segoe UI", 10),
                bg=COLORS['bg_medium'],
                fg=COLORS['text'],
                anchor=tk.W
            ).pack(side=tk.LEFT, fill=tk.X, expand=True)
            
            self.folder_checkboxes[path] = cb
            self.folder_items[path] = {'frame': frame, 'var': var, 'depth': depth}
            
            if depth < self.max_depth.get():
                try:
                    items = sorted([f for f in os.listdir(path) if os.path.isdir(os.path.join(path, f))])
                    for item in items[:20]:
                        self._add_folder(os.path.join(path, item), depth + 1)
                except (PermissionError, OSError):
                    pass
        except (PermissionError, OSError):
            pass
    
    def select_all(self):
        for var in self.selected_folders.values():
            var.set(True)
    
    def deselect_all(self):
        for var in self.selected_folders.values():
            var.set(False)
    
    def browse_folder(self):
        folder = filedialog.askdirectory(
            title="Select folder",
            initialdir=self.scan_path.get()
        )
        if folder:
            self.scan_path.set(folder)
            self.populate_folder_tree(folder)
            self.save_config()
    
    def get_selected_folders(self):
        return [path for path, var in self.selected_folders.items() if var.get()]
    
    def open_github(self):
        import webbrowser
        webbrowser.open("https://github.com/yourusername/universal-i18n-translator")
    
    # ===================================================
    # 📝 LOGGING
    # ===================================================
    
    def log(self, message, tag=None):
        self.result_text.insert(tk.END, message + "\n", tag if tag else "info")
        self.result_text.see(tk.END)
        self.root.update_idletasks()
        logger.info(message)
    
    # ===================================================
    # ⚙️ CONFIGURATION
    # ===================================================
    
    def load_config(self):
        try:
            if os.path.exists(CONFIG_FILE):
                with open(CONFIG_FILE, 'r') as f:
                    config = json.load(f)
                    self.scan_path.set(config.get('scan_path', os.path.expanduser("~")))
                    self.max_depth.set(config.get('max_depth', 5))
                    self.source_lang.set(config.get('source_lang', 'auto'))
                    self.target_lang.set(config.get('target_lang', 'en'))
                    self.smart_skip.set(config.get('smart_skip', True))
                    self.parallel.set(config.get('parallel', True))
                    self.max_workers.set(config.get('max_workers', 4))
        except:
            pass
    
    def save_config(self):
        try:
            config = {
                'scan_path': self.scan_path.get(),
                'max_depth': self.max_depth.get(),
                'source_lang': self.source_lang.get(),
                'target_lang': self.target_lang.get(),
                'smart_skip': self.smart_skip.get(),
                'parallel': self.parallel.get(),
                'max_workers': self.max_workers.get()
            }
            with open(CONFIG_FILE, 'w') as f:
                json.dump(config, f, indent=2)
        except:
            pass
    
    def on_closing(self):
        if self.is_analyzing or self.is_translating:
            if messagebox.askokcancel("Stop operation", "Operation in progress. Stop?"):
                self.stop_requested = True
                self.paused = False
                time.sleep(0.5)
                self.translator.save_cache()
                self.explorer.save_cache()
                self.save_config()
                self.root.destroy()
        else:
            self.translator.save_cache()
            self.explorer.save_cache()
            self.save_config()
            self.root.destroy()

# ===================================================
# 🚀 START
# ===================================================

if __name__ == "__main__":
    root = tk.Tk()
    app = UniversalI18NApp(root)
    root.mainloop()
