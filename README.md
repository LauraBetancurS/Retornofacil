# RetornoFácil — Landing de test

Archivos: `index.html` (la página) y `hero.jpg` (imagen del banner). No necesita instalar nada.

## 1. Configurar (arriba en `index.html`, bloque `RF_CONFIG`)

```js
GA_ID: 'G-XXXXXXXXXX',   // Google Analytics 4
CLARITY_ID: '',          // opcional: Microsoft Clarity (mapas de calor + hasta dónde scrollea la gente)
FORM_ENDPOINT: ''        // Google Apps Script que guarda los registros en una hoja
```

**Google Analytics 4:** analytics.google.com → Administrar → Crear propiedad → Flujo de datos "Web" con la URL de Vercel → copia el ID `G-…`.

**Clarity (opcional, gratis):** clarity.microsoft.com → nuevo proyecto → copia el ID. Sirve para ver grabaciones y hasta dónde scrollea la gente.

**Guardar los registros en Google Sheets:**
1. Crea una hoja de cálculo → Extensiones → Apps Script → pega esto:
```js
function doPost(e) {
  var sh = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
  var p = e.parameter;
  if (sh.getLastRow() === 0) sh.appendRow(Object.keys(p));
  var head = sh.getRange(1, 1, 1, sh.getLastColumn()).getValues()[0];
  Object.keys(p).forEach(function (k) { if (head.indexOf(k) < 0) { head.push(k); sh.getRange(1, head.length).setValue(k); } });
  sh.appendRow(head.map(function (k) { return p[k] || ''; }));
  return ContentService.createTextOutput('ok');
}
```
2. Implementar → Nueva implementación → tipo "Aplicación web" → Ejecutar como: tú → Acceso: Cualquier persona → copia la URL y pégala en `FORM_ENDPOINT`.

## 2. Publicar en Vercel
Sube esta carpeta a un repo de GitHub → vercel.com → Add New Project → importa el repo → Deploy (sin configuración extra). O arrastra la carpeta en vercel.com/new.

## 3. Qué se mide (eventos en GA4)
| Evento | Qué te dice |
|---|---|
| `cta_click` (`cta_location`: nav, top, flotante, planes…) | Quién da clic en "Quiero probarlo gratis" y desde dónde |
| `plan_click` (`plan`) | Qué plan/precio atrae más |
| `section_view` (`section`) | Embudo: hasta qué sección llega la gente |
| `scroll_depth` (25/50/75/90/100) | Cuánto scrollean |
| `time_on_page` (15/30/60/120/300 s) | Tiempo en la página |
| `form_start`, `form_error`, `generate_lead` | Quién empieza, se equivoca y termina el registro |
| `faq_open` | Qué dudas tienen |

Cada evento lleva `seconds_on_page` (así ves si alguien se inscribió sin scrollear o después de leer todo).
En GA4: Informes → Participación → Eventos. Marca `generate_lead` como evento clave (conversión).

**Origen del tráfico:** usa enlaces con UTM en cada anuncio o DM, por ejemplo
`https://tu-landing.vercel.app/?utm_source=instagram&utm_medium=dm&utm_campaign=piloto`
(`utm_source` = facebook / google / instagram…). La UTM queda guardada con cada registro en la hoja.
