# Bolsa de valores.
## NOMBRE Y NÚMERO DE CONTROL DEL ALUMNOS:
Huerta Rivas Olaf Alexandro 21211966  
Vara Espinoza Ulises Andres 21212656  
Molina Fabela Edgar Fabian 22210180  

## Introducción:
El proyecto FinancialDashboard desarrollado en ASP.NET Core MVC es una aplicación web interactiva que integra un backend en C# y un frontend dinámico en JavaScript. Su propósito es ofrecer una simulación de análisis financiero en tiempo real, permitiendo al usuario visualizar datos de acciones, índices bursátiles y métricas personalizadas mediante gráficos dinámicos, además de incluir funciones de autenticación, conversión de divisas y cálculo de inversiones. Esta estructura combina los principios del patrón Modelo–Vista–Controlador (MVC) para garantizar una arquitectura organizada, escalable y fácil de mantener, donde los Models gestionan los datos, los Controllers controlan la lógica y las Views presentan la interfaz al usuario.

## Objetivo:
El objetivo del proyecto es crear un panel financiero funcional y educativo que permita a los usuarios practicar análisis de datos económicos en un entorno simulado, comprendiendo la dinámica entre el frontend y el backend dentro del marco ASP.NET Core MVC. Además, busca demostrar la correcta integración entre la generación de datos en el servidor, la representación visual en el navegador y la interacción del usuario mediante interfaces modernas e intuitivas.

Opción 1: ASP.NET Core MVC
FinancialDashboard/

├── Controllers/  
│   └── HomeController.cs   
├── Models  
│   ├── LoginModel.cs   
│   ├── FinancialData.cs  
│   └── CurrencyModel.cs     
├── Views/  
│   ├── Home/  
│   │   └── Index.cshtml   
│   └── Shared/  
│       └── _Layout.cshtml   
├── wwwroot/  
│   └── css/  
│       └── site.css  
└── Program.cs

1. Models/LoginModel.cs
```
namespace FinancialDashboard.Models
{
    public class LoginModel
    {
        public string Username { get; set; }
        public string Password { get; set; }
    }
}
```

2. Models/FinancialData.cs
```
namespace FinancialDashboard.Models
{
    public class FinancialData
    {
        public string EntityName { get; set; }
        public string TabType { get; set; }
        public string Period { get; set; }
        public List<decimal> DataPoints { get; set; }
        public string Description { get; set; }
        public decimal CurrentValue { get; set; }
    }

    public class CurrencyData
    {
        public decimal MXN { get; set; } = 20.00m;
        public decimal USD { get; set; } = 1.00m;
        public decimal EUR { get; set; } = 0.92m;
    }
}
```
3. Controllers/HomeController.cs
```
using Microsoft.AspNetCore.Mvc;
using FinancialDashboard.Models;
using System.Text.Json;

namespace FinancialDashboard.Controllers
{
    public class HomeController : Controller
    {
        private readonly List<string> _accionesList = new()
        {
            "Apple", "Microsoft", "Google", "Amazon", "Tesla", 
            "Meta", "Nvidia", "Intel", "Netflix", "Samsung"
        };

        private readonly List<string> _bolsasList = new()
        {
            "NASDAQ", "NYSE", "BMV", "IBEX 35", "Nikkei", 
            "FTSE 100", "DAX", "CAC 40", "TSX", "BSE Sensex"
        };

        private readonly List<string> _ibisesList = new()
        {
            "Ibis escarlata", "Ibis blanco", "Ibis negro", 
            "Ibis australiano", "Ibis sagrado"
        };

        public IActionResult Index()
        {
            return View();
        }

        [HttpPost]
        public IActionResult Login([FromBody] LoginModel login)
        {
            if (login.Username == "admin" && login.Password == "1234")
            {
                return Json(new { success = true });
            }
            return Json(new { success = false, error = "Usuario o contraseña incorrectos." });
        }

        [HttpGet]
        public IActionResult GetFinancialData(string tab, string entity, string period)
        {
            var data = GenerateFinancialData(tab, entity, period);
            return Json(data);
        }

        [HttpGet]
        public IActionResult GetCurrencyData()
        {
            var currencyData = new CurrencyData();
            return Json(currencyData);
        }

        [HttpPost]
        public IActionResult ConvertCurrency([FromBody] CurrencyConversionRequest request)
        {
            var currencies = new CurrencyData();
            decimal amountInUsd;

            // Convert to USD first
            if (request.FromCurrency == "USD")
                amountInUsd = request.Amount;
            else
                amountInUsd = request.Amount / GetCurrencyRate(request.FromCurrency, currencies);

            // Convert from USD to target currency
            decimal result = amountInUsd * GetCurrencyRate(request.ToCurrency, currencies);

            return Json(new { result = result });
        }

        private decimal GetCurrencyRate(string currency, CurrencyData currencies)
        {
            return currency switch
            {
                "MXN" => currencies.MXN,
                "USD" => currencies.USD,
                "EUR" => currencies.EUR,
                _ => 1.00m
            };
        }

        private FinancialData GenerateFinancialData(string tab, string entity, string period)
        {
            var random = new Random();
            var dataPoints = new List<decimal>();
            int pointsCount = period switch
            {
                "dia" => 30,
                "mes" => 12,
                "anio" => 5,
                _ => 30
            };

            decimal baseValue = GetBaseValue(tab, entity);
            decimal currentValue = baseValue;

            for (int i = 0; i < pointsCount; i++)
            {
                decimal variation = (decimal)(random.NextDouble() * 0.06 - 0.03);
                currentValue = Math.Max(0.01m, currentValue * (1 + variation));
                dataPoints.Add(Math.Round(currentValue, 2));
            }

            return new FinancialData
            {
                EntityName = entity,
                TabType = tab,
                Period = period,
                DataPoints = dataPoints,
                CurrentValue = dataPoints.Last(),
                Description = GenerateDescription(tab, entity)
            };
        }

        private decimal GetBaseValue(string tab, string entity)
        {
            var random = new Random(entity.GetHashCode());
            return tab switch
            {
                "acciones" => random.Next(50, 500),
                "bolsa" => random.Next(5000, 50000),
                "ibises" => random.Next(1000, 15000),
                _ => 100
            };
        }

        private string GenerateDescription(string tab, string entity)
        {
            return tab switch
            {
                "acciones" => $"{entity} es una empresa listada en mercados internacionales. Datos y métricas son simuladas.",
                "bolsa" => $"{entity} es un mercado/índice global. Datos y métricas son simuladas.",
                "ibises" => $"{entity} — Vista de métrica (simulada) por Día / Mes / Año.",
                _ => "Descripción no disponible."
            };
        }
    }

    public class CurrencyConversionRequest
    {
        public decimal Amount { get; set; }
        public string FromCurrency { get; set; }
        public string ToCurrency { get; set; }
    }
}
```

4. Views/Home/Index.cshtml
```
@{
    ViewData["Title"] = "Dashboard Financiero";
}

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8" />
    <title>@ViewData["Title"]</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="stylesheet" href="~/css/site.css" />
</head>
<body>

    <!-- LOGIN -->
    <div id="login" class="login">
        <h2 class="login-title">Iniciar Sesión</h2>
        <input type="text" id="username" placeholder="Usuario" />
        <br />
        <input type="password" id="password" placeholder="Contraseña" />
        <br />
        <button onclick="login()">Entrar</button>
        <p id="login-error" class="error-message"></p>
    </div>

    <!-- DASHBOARD -->
    <div id="dashboard" class="dashboard hidden">
        <h1>Panel Financiero — Acciones, Bolsa e Ibises</h1>

        <!-- Tabs -->
        <div class="tabs">
            <button class="tab-btn active" id="tab-acciones" onclick="cambiarTab('acciones')">Acciones</button>
            <button class="tab-btn" id="tab-bolsa" onclick="cambiarTab('bolsa')">Bolsa de Valores</button>
            <button class="tab-btn" id="tab-ibises" onclick="cambiarTab('ibises')">Tipos de Ibises</button>
        </div>

        <!-- Entity Buttons -->
        <div id="buttonsWrapper" class="buttons"></div>

        <!-- Period selector -->
        <div class="periods">
            <button class="period-btn active" data-period="dia" onclick="cambiarPeriodo(event)">Día</button>
            <button class="period-btn" data-period="mes" onclick="cambiarPeriodo(event)">Mes</button>
            <button class="period-btn" data-period="anio" onclick="cambiarPeriodo(event)">Año</button>
        </div>

        <div class="content-wrapper">
            <!-- Gráfico -->
            <div class="chart-container">
                <canvas id="accionesChart"></canvas>
            </div>

            <!-- Descripción e inversión -->
            <div class="company-info" id="companyInfo">
                <h2>Selecciona un elemento</h2>
                <p>Verás aquí la información detallada del elemento seleccionado.</p>
            </div>
        </div>

        <!-- MONEDAS -->
        <div class="monedas-section">
            <h2>Estado Actual de Monedas</h2>
            <div class="valores-monedas">
                <div class="valor-moneda" id="valorMXN">
                    Peso Mexicano (MXN)<br><strong>$20.00</strong>
                    <span>Por dólar</span>
                </div>
                <div class="valor-moneda" id="valorUSD">
                    Dólar Estadounidense (USD)<br><strong>$1.00</strong>
                    <span>Referencia base</span>
                </div>
                <div class="valor-moneda" id="valorEUR">
                    Euro (EUR)<br><strong>0.92</strong>
                    <span>Por dólar</span>
                </div>
            </div>

            <div class="conversor">
                <label for="cantidad">Cantidad:</label>
                <input type="number" id="cantidad" min="0" step="0.01" placeholder="Ejemplo: 100" />

                <label for="monedaOrigen">De:</label>
                <select id="monedaOrigen">
                    <option value="MXN">Peso Mexicano (MXN)</option>
                    <option value="USD" selected>Dólar Estadounidense (USD)</option>
                    <option value="EUR">Euro (EUR)</option>
                </select>

                <label for="monedaDestino">A:</label>
                <select id="monedaDestino">
                    <option value="MXN">Peso Mexicano (MXN)</option>
                    <option value="USD">Dólar Estadounidense (USD)</option>
                    <option value="EUR" selected>Euro (EUR)</option>
                </select>

                <button onclick="convertirMoneda()">Convertir</button>

                <div class="resultado-conversion" id="resultadoConversion"></div>
            </div>
        </div>

        <footer>© 2025 Panel Financiero Interactivo</footer>
    </div>

    <script src="~/js/site.js"></script>
</body>
</html>
```
5. wwwroot/css/site.css
```
:root {
    --primary-color: #6a00ff;
    --primary-dark: #4b00b2;
    --secondary-color: #28a745;
    --secondary-dark: #1e7e34;
    --background-color: #eae6fc;
    --card-bg: #ffffff;
    --text-color: #333333;
    --light-text: #888888;
}

* { box-sizing: border-box; }
body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: var(--background-color);
    margin: 0;
    padding: 0;
    color: var(--text-color);
}

.login, .dashboard { max-width: 1100px; margin: auto; padding: 40px 20px; }
.login {
    text-align: center;
    margin-top: 60px;
    background: var(--card-bg);
    border-radius: 15px;
    box-shadow: 0 10px 30px rgba(0,0,0,0.1);
    padding: 40px;
}
input { padding: 12px; width: 80%; margin: 10px 0; border: none; border-radius: 10px; outline: none; box-shadow: inset 2px 2px 5px #ccc, inset -2px -2px 5px #fff; font-size: 16px; }
button { background: var(--primary-color); color: white; padding: 12px 25px; border: none; border-radius: 10px; cursor: pointer; margin-top: 15px; transition: background-color 0.3s ease; font-size: 16px; font-weight: 600; }
button:hover { background-color: var(--primary-dark); }
.hidden { display: none !important; }

.dashboard h1 { text-align: center; margin-bottom: 20px; color: var(--text-color); }

/* Tabs */
.tabs {
    display: flex;
    gap: 12px;
    justify-content: center;
    margin-bottom: 20px;
    flex-wrap: wrap;
}
.tab-btn {
    padding: 10px 18px;
    border-radius: 10px;
    background: #f5f0ff;
    border: 2px solid var(--primary-color);
    color: var(--primary-color);
    font-weight: 700;
    cursor: pointer;
}
.tab-btn.active { background: var(--primary-color); color: white; }

.buttons {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
    justify-content: center;
    margin-bottom: 18px;
}
.entity-btn {
    padding: 10px 14px;
    font-size: 14px;
    background: #f5f0ff;
    color: var(--primary-color);
    border: 2px solid var(--primary-color);
    border-radius: 10px;
    cursor: pointer;
    transition: 0.2s;
    flex: 0 1 auto;
    font-weight: 600;
}
.entity-btn:hover, .entity-btn.active { background: var(--primary-color); color: white; }

.periods {
    display:flex;
    gap:8px;
    justify-content:center;
    margin-bottom:18px;
}
.period-btn {
    padding: 8px 12px;
    border-radius: 8px;
    background: #fff;
    border: 1px solid #ddd;
    cursor: pointer;
    font-weight:600;
}
.period-btn.active { background: var(--secondary-color); color: white; border-color: var(--secondary-color); }

/* Layout flex para gráfico + descripción + inversión */
.content-wrapper {
    display: flex;
    gap: 30px;
    justify-content: center;
    align-items: flex-start;
    flex-wrap: wrap;
    margin-bottom: 40px;
}
.chart-container {
    background: var(--card-bg);
    border-radius: 15px;
    padding: 20px;
    box-shadow: 0 10px 25px rgba(0,0,0,0.1);
    flex: 1 1 620px;
    max-width: 620px;
    min-width: 320px;
    position: relative;
    height: 420px;
}
.company-info {
    background: var(--card-bg);
    padding: 20px;
    border-radius: 15px;
    box-shadow: 0 10px 25px rgba(0,0,0,0.1);
    max-width: 420px;
    flex: 1 1 360px;
    color: var(--text-color);
    display: flex;
    flex-direction: column;
    gap: 15px;
}
.company-info h2 { margin-top: 0; color: var(--primary-color); }
.company-info p { line-height: 1.5; }
.company-info .data-row { margin: 10px 0; font-weight: 600; display: flex; justify-content: space-between; border-bottom: 1px solid #eee; padding-bottom: 6px; }

.inversion-section { border-top: 1px solid #ddd; padding-top: 15px; }
.inversion-section label { display: block; margin-bottom: 6px; font-weight: 600; color: var(--primary-color); }
.inversion-section input[type="number"] { width: 100%; padding: 10px; border-radius: 8px; border: 1px solid #ccc; box-sizing: border-box; font-size: 16px; margin-bottom: 10px; }
.inversion-section button { width: 100%; background: var(--secondary-color); border: none; color: white; font-weight: 600; font-size: 16px; padding: 12px; border-radius: 10px; cursor: pointer; transition: background-color 0.3s ease; }
.inversion-section button:hover { background-color: var(--secondary-dark); }
.resultado-inversion { margin-top: 15px; font-weight: 700; font-size: 18px; color: var(--text-color); }

/* Monedas */
.monedas-section {
    max-width: 960px;
    background: var(--card-bg);
    margin: auto;
    border-radius: 15px;
    padding: 20px 30px;
    box-shadow: 0 10px 25px rgba(0,0,0,0.1);
    margin-bottom: 50px;
}
.monedas-section h2 { color: var(--primary-color); margin-bottom: 15px; text-align: center; }
.valores-monedas { display: flex; justify-content: space-around; margin-bottom: 30px; font-size: 18px; font-weight: 600; color: var(--text-color); }
.valor-moneda { background: #f0edff; border-radius: 10px; padding: 15px 20px; flex: 1 1 200px; margin: 0 10px; text-align: center; box-shadow: inset 2px 2px 6px #ddd, inset -2px -2px 6px #fff; }
.valor-moneda span { display: block; font-size: 14px; color: #666; margin-top: 5px; }

.conversor { max-width: 480px; margin: auto; background: #f7f5ff; border-radius: 15px; padding: 20px 25px; box-shadow: 0 5px 15px rgba(0,0,0,0.1); color: var(--text-color); }
.conversor label { font-weight: 600; color: var(--primary-color); margin-top: 15px; display: block; }
.conversor input[type="number"], .conversor select { width: 100%; padding: 10px; border-radius: 8px; border: 1px solid #ccc; margin-top: 5px; font-size: 16px; box-sizing: border-box; }
.conversor button { margin-top: 20px; background: var(--primary-color); border: none; color: white; font-weight: 600; font-size: 18px; padding: 12px; border-radius: 10px; cursor: pointer; transition: background-color 0.3s ease; width: 100%; }
.conversor button:hover { background-color: var(--primary-dark); }
.resultado-conversion { margin-top: 20px; font-size: 20px; font-weight: 700; text-align: center; color: var(--text-color); min-height: 28px; }

footer { text-align: center; margin-top: 40px; color: var(--light-text); }

@media (max-width: 1000px) {
    .content-wrapper { flex-direction: column; align-items: center; }
    .chart-container, .company-info { max-width: 100%; flex: unset; width: 100%; }
    .valores-monedas { flex-direction: column; gap: 15px; }
    .valor-moneda { margin: 0; }
}
.login-title { color: var(--primary-color); margin-bottom: 30px; }
.error-message { color: #ff3860; margin-top: 10px; font-weight: 600; }
```

6. wwwroot/js/site.js
```
// -------- Estado UI y Chart.js --------
let activeTab = 'acciones';
let activeEntity = null;
let activePeriod = 'dia';
let chart = null;

// Listas de entidades
const ACCIONES_LIST = ["Apple", "Microsoft", "Google", "Amazon", "Tesla", "Meta", "Nvidia", "Intel", "Netflix", "Samsung"];
const BOLSAS_LIST = ["NASDAQ", "NYSE", "BMV", "IBEX 35", "Nikkei", "FTSE 100", "DAX", "CAC 40", "TSX", "BSE Sensex"];
const IBISES_LIST = ["Ibis escarlata", "Ibis blanco", "Ibis negro", "Ibis australiano", "Ibis sagrado"];

// -------- Login --------
async function login() {
    const user = document.getElementById("username").value;
    const pass = document.getElementById("password").value;
    const error = document.getElementById("login-error");

    try {
        const response = await fetch('/Home/Login', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({ username: user, password: pass })
        });

        const result = await response.json();

        if (result.success) {
            document.getElementById("login").classList.add("hidden");
            document.getElementById("dashboard").classList.remove("hidden");
            inicializarPanel();
            cargarValoresMonedas();
        } else {
            error.textContent = result.error;
        }
    } catch (error) {
        console.error('Error en login:', error);
        error.textContent = "Error de conexión.";
    }
}

document.getElementById("password").addEventListener("keypress", function(event) {
    if (event.key === "Enter") login();
});

function getEntitiesForTab(tab) {
    if (tab === 'acciones') return ACCIONES_LIST;
    if (tab === 'bolsa') return BOLSAS_LIST;
    return IBISES_LIST;
}

function renderButtonsForTab(tab) {
    const wrapper = document.getElementById('buttonsWrapper');
    wrapper.innerHTML = '';
    const list = getEntitiesForTab(tab);
    list.forEach(name => {
        const btn = document.createElement('button');
        btn.className = 'entity-btn';
        btn.textContent = name;
        btn.onclick = () => seleccionarEntidad(name);
        wrapper.appendChild(btn);
    });
    
    document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
    document.getElementById('tab-' + (tab === 'bolsa' ? 'bolsa' : tab)).classList.add('active');
    
    document.querySelectorAll('.period-btn').forEach(b => b.classList.remove('active'));
    document.querySelector('.periods .period-btn[data-period="dia"]').classList.add('active');
    activePeriod = 'dia';
}

function cambiarTab(tab) {
    if (activeTab === tab) return;
    activeTab = tab;
    
    document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
    if (tab === 'acciones') document.getElementById('tab-acciones').classList.add('active');
    if (tab === 'bolsa') document.getElementById('tab-bolsa').classList.add('active');
    if (tab === 'ibises') document.getElementById('tab-ibises').classList.add('active');
    
    renderButtonsForTab(tab);
    const first = getEntitiesForTab(tab)[0];
    seleccionarEntidad(first);
}

function cambiarPeriodo(e) {
    const btn = e.currentTarget;
    const periodo = btn.getAttribute('data-period');
    document.querySelectorAll('.period-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    activePeriod = periodo;
    
    if (activeEntity) mostrarGrafico(activeEntity);
}

async function seleccionarEntidad(name) {
    activeEntity = name;
    
    document.querySelectorAll('.entity-btn').forEach(b => {
        b.classList.toggle('active', b.textContent === name);
    });
    
    await mostrarGrafico(name);
    await actualizarPanelInfo(name);
}

function generarLabels(periodo) {
    if (periodo === 'dia') {
        const labels = [];
        for (let i = 30; i >= 1; i--) labels.push(`${i}d`);
        return labels;
    } else if (periodo === 'mes') {
        const months = ["Ene","Feb","Mar","Abr","May","Jun","Jul","Ago","Sep","Oct","Nov","Dic"];
        return months.slice(0,12);
    } else {
        const currentYear = new Date().getFullYear();
        const years = [];
        for (let i = 4; i >= 0; i--) years.push(String(currentYear - i));
        return years;
    }
}

async function mostrarGrafico(entityName) {
    try {
        const response = await fetch(`/Home/GetFinancialData?tab=${activeTab}&entity=${entityName}&period=${activePeriod}`);
        const data = await response.json();
        
        const ctx = document.getElementById('accionesChart').getContext('2d');
        const labels = generarLabels(activePeriod);
        
        const displayData = data.dataPoints.slice(-labels.length);
        while (displayData.length < labels.length) {
            displayData.unshift(displayData[0] || 0);
        }

        const segments = displayData.map((value, idx, arr) => {
            if (idx === 0) return 'green';
            return value >= arr[idx-1] ? 'green' : 'red';
        });
        
        const lastColor = displayData.length > 1 && displayData[displayData.length-1] >= displayData[displayData.length-2] ? 'green' : 'red';

        if (chart) chart.destroy();
        
        chart = new Chart(ctx, {
            type: 'line',
            data: {
                labels: labels,
                datasets: [{
                    label: `${activeTab === 'acciones' ? 'Acciones' : (activeTab === 'bolsa' ? 'Índice' : 'Métrica')} — ${entityName} (${activePeriod})`,
                    data: displayData,
                    borderColor: lastColor,
                    backgroundColor: 'rgba(0,0,0,0)',
                    fill: false,
                    tension: 0.25,
                    pointRadius: 6,
                    pointBackgroundColor: segments,
                    pointBorderColor: segments,
                    pointHoverRadius: 8,
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    y: {
                        beginAtZero: false,
                        grid: { color: '#ccc' },
                        ticks: { color: '#333' }
                    },
                    x: {
                        grid: { color: '#eee' },
                        ticks: { color: '#333' }
                    }
                },
                plugins: {
                    legend: {
                        labels: {
                            color: '#333',
                            font: { weight: 'bold', size: 14 }
                        }
                    },
                    tooltip: {
                        callbacks: {
                            label: function(context) {
                                const val = context.formattedValue;
                                if (activeTab === 'ibises') return `Métrica: ${val}`;
                                return `Valor: ${val}`;
                            }
                        }
                    }
                }
            }
        });
    } catch (error) {
        console.error('Error al cargar gráfico:', error);
    }
}

async function actualizarPanelInfo(name) {
    try {
        const response = await fetch(`/Home/GetFinancialData?tab=${activeTab}&entity=${name}&period=${activePeriod}`);
        const data = await response.json();
        
        const infoDiv = document.getElementById("companyInfo");
        
        if (activeTab === 'acciones' || activeTab === 'bolsa') {
            infoDiv.innerHTML = `
                <h2>${name}</h2>
                <p>${data.description}</p>
                <div class="data-row"><span>Sector:</span><span>Tecnología / Servicios</span></div>
                <div class="data-row"><span>Capitalización:</span><span>$${(Math.random()*200 + 10).toFixed(1)}B</span></div>
                <div class="data-row"><span>Empleados:</span><span>${Math.floor(Math.random()*90000 + 1000).toLocaleString()}</span></div>
                <div class="data-row"><span>Mercado:</span><span>${activeTab === 'acciones' ? 'NASDAQ / NYSE / BMV' : name}</span></div>
                <div class="inversion-section">
                    <label for="montoInversion">Monto a invertir en USD:</label>
                    <input type="number" id="montoInversion" min="0" step="0.01" placeholder="Ejemplo: 1000" />
                    <button onclick="calcularInversion()">Calcular Valor Actual</button>
                    <div class="resultado-inversion" id="resultadoInversion"></div>
                </div>
            `;
        } else {
            infoDiv.innerHTML = `
                <h2>${name}</h2>
                <p>${data.description}</p>
                <div class="data-row"><span>Métrica actual:</span><span>${data.currentValue}</span></div>
                <div class="data-row"><span>Periodo:</span><span>${activePeriod}</span></div>
                <div style="margin-top:10px;font-size:14px;color:#666">Nota: Datos simulados</div>
            `;
        }
    } catch (error) {
        console.error('Error al cargar información:', error);
    }
}

function calcularInversion() {
    const montoEl = document.getElementById("montoInversion");
    if (!montoEl) return;
    const monto = parseFloat(montoEl.value);
    const resultado = document.getElementById("resultadoInversion");

    if (isNaN(monto) || monto <= 0) {
        resultado.textContent = "Por favor ingresa un monto válido.";
        resultado.style.color = "#ff3860";
        return;
    }

    // En una implementación real, esto vendría del servidor
    const precio = 100; // Valor simulado
    const valorActual = monto * precio;
    resultado.textContent = `Con una inversión de $${monto.toFixed(2)} USD, el valor actual es $${valorActual.toFixed(2)} USD. (Precio base: ${precio})`;
    resultado.style.color = "#333";
}

// -------- Monedas --------
async function cargarValoresMonedas() {
    try {
        const response = await fetch('/Home/GetCurrencyData');
        const data = await response.json();
        
        document.getElementById("valorMXN").querySelector('strong').textContent = `$${data.mxn.toFixed(2)}`;
        document.getElementById("valorUSD").querySelector('strong').textContent = `$${data.usd.toFixed(2)}`;
        document.getElementById("valorEUR").querySelector('strong').textContent = `${data.eur.toFixed(2)}`;
    } catch (error) {
        console.error('Error al cargar monedas:', error);
    }
}

async function convertirMoneda() {
    const cantidad = parseFloat(document.getElementById("cantidad").value);
    const origen = document.getElementById("monedaOrigen").value;
    const destino = document.getElementById("monedaDestino").value;
    const resultadoDiv = document.getElementById("resultadoConversion");

    if (isNaN(cantidad) || cantidad <= 0) {
        resultadoDiv.textContent = "Ingrese una cantidad válida.";
        resultadoDiv.style.color = "#ff3860";
        return;
    }
    if (origen === destino) {
        resultadoDiv.textContent = "Seleccione monedas diferentes para convertir.";
        resultadoDiv.style.color = "#ff3860";
        return;
    }

    try {
        const response = await fetch('/Home/ConvertCurrency', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({
                amount: cantidad,
                fromCurrency: origen,
                toCurrency: destino
            })
        });

        const result = await response.json();
        resultadoDiv.textContent = `${cantidad.toFixed(2)} ${origen} = ${result.result.toFixed(2)} ${destino}`;
        resultadoDiv.style.color = "#333";
    } catch (error) {
        console.error('Error en conversión:', error);
        resultadoDiv.textContent = "Error en la conversión.";
        resultadoDiv.style.color = "#ff3860";
    }
}

function inicializarPanel() {
    renderButtonsForTab(activeTab);
    const first = getEntitiesForTab(activeTab)[0];
    seleccionarEntidad(first);
}
```

7. Program.cs
```
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllersWithViews();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Home/Error");
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();

app.UseRouting();

app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

app.Run();
```
Para ejecutar en VS Code:
1. Crea el proyecto:
```
dotnet new mvc -n FinancialDashboard
cd FinancialDashboard
```
2. Reemplaza los archivos con el código proporcionado.
3. Instala las dependencias:
```
dotnet add package Chart.js
```
4. Ejecuta la aplicación:
```
dotnet run
```
Abre tu navegador en https://localhost:7000 (o el puerto que te indique)
Credenciales de login:
Usuario: admin

Contraseña: 1234

Esta implementación mantiene toda la funcionalidad original pero ahora está estructurada como una aplicación ASP.NET Core MVC con backend en C# y frontend en JavaScript. Los datos se generan dinámicamente en el servidor y se comunican mediante APIs REST.





