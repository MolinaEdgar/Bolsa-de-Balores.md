[bolsa2.html](https://github.com/user-attachments/files/23226250/bolsa2.html)
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>Dashboard Acciones, Bolsa e Ibises</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <!-- Chart.js -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
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
  </style>
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
    <h1>Panel Financiero — Acciones, Bolsa y Ibises</h1>

    <!-- Tabs -->
    <div class="tabs">
      <button class="tab-btn active" id="tab-acciones" onclick="cambiarTab('acciones')">Acciones</button>
      <button class="tab-btn" id="tab-bolsa" onclick="cambiarTab('bolsa')">Bolsa de Valores</button>
      <button class="tab-btn" id="tab-ibises" onclick="cambiarTab('ibises')">Tipos de Ibises</button>
    </div>

    <!-- Entity Buttons (se rellenan dinámicamente) -->
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

    <!-- NUEVO APARTADO MONEDAS -->
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

  <script>
    // -------- Login simple (mantener admin / 1234) --------
    function login() {
      const user = document.getElementById("username").value;
      const pass = document.getElementById("password").value;
      const error = document.getElementById("login-error");

      if (user === "admin" && pass === "1234") {
        document.getElementById("login").classList.add("hidden");
        document.getElementById("dashboard").classList.remove("hidden");
        inicializarPanel();
        cargarValoresMonedas(); // cargar valores al inicio
      } else {
        error.textContent = "Usuario o contraseña incorrectos.";
      }
    }
    document.getElementById("password").addEventListener("keypress", function(event) {
      if (event.key === "Enter") login();
    });

    // -------- Datos base (simulados aleatoriamente) --------
    // Listas de entidades
    const ACCIONES_LIST = ["Apple", "Microsoft", "Google", "Amazon", "Tesla", "Meta", "Nvidia", "Intel", "Netflix", "Samsung"];
    const BOLSAS_LIST = ["NASDAQ", "NYSE", "BMV", "IBEX 35", "Nikkei", "FTSE 100", "DAX", "CAC 40", "TSX", "BSE Sensex"];
    const IBISES_LIST = ["Ibis escarlata", "Ibis blanco", "Ibis negro", "Ibis australiano", "Ibis sagrado"];

    // Generador de datos: random walk
    function generarSerieRandomWalk(puntos, base, variacionPercent=0.03) {
      const arr = [];
      let val = base;
      for (let i = 0; i < puntos; i++) {
        // cambio aleatorio proporcional
        const cambio = (Math.random() * 2 - 1) * variacionPercent; // -variacion..+variacion
        val = Math.max(0.01, val * (1 + cambio));
        // redondeo razonable
        arr.push(parseFloat(val.toFixed(2)));
      }
      return arr;
    }

    // Crear dataset simulado para cada entidad y periodo
    const datosSimulados = {
      acciones: {},
      bolsas: {},
      ibises: {}
    };

    // Semillas base (valores iniciales razonables)
    const baseAcciones = [170, 340, 2800, 3200, 200, 320, 500, 40, 450, 60];
    const baseBolsas = [15000, 35000, 52000, 9000, 30000, 7200, 15200, 6500, 20000, 60000];
    const baseIbises = [5000, 12000, 3000, 7000, 9000]; // métrica tipo 'valor' similar a precio/índice

    function generarTodosLosDatos() {
      // Acciones
      ACCIONES_LIST.forEach((name, i) => {
        datosSimulados.acciones[name] = {
          dia: generarSerieRandomWalk(30, baseAcciones[i], 0.03),
          mes: generarSerieRandomWalk(12, baseAcciones[i], 0.06),
          anio: generarSerieRandomWalk(5, baseAcciones[i], 0.12)
        };
      });
      // Bolsas
      BOLSAS_LIST.forEach((name, i) => {
        datosSimulados.bolsas[name] = {
          dia: generarSerieRandomWalk(30, baseBolsas[i], 0.02),
          mes: generarSerieRandomWalk(12, baseBolsas[i], 0.05),
          anio: generarSerieRandomWalk(5, baseBolsas[i], 0.10)
        };
      });
      // Ibises (métrica tipo acciones)
      IBISES_LIST.forEach((name, i) => {
        datosSimulados.ibises[name] = {
          dia: generarSerieRandomWalk(30, baseIbises[i], 0.05),
          mes: generarSerieRandomWalk(12, baseIbises[i], 0.08),
          anio: generarSerieRandomWalk(5, baseIbises[i], 0.15)
        };
      });
    }

    // -------- Estado UI y Chart.js --------
    let activeTab = 'acciones'; // 'acciones' | 'bolsa' | 'ibises'
    let activeEntity = null;
    let activePeriod = 'dia'; // 'dia' | 'mes' | 'anio'
    let chart = null;

    function inicializarPanel() {
      generarTodosLosDatos();
      renderButtonsForTab(activeTab);
      // seleccionar primer elemento por defecto
      const first = getEntitiesForTab(activeTab)[0];
      seleccionarEntidad(first);
    }

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
      // actualizar estilos de pestañas
      document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
      document.getElementById('tab-' + (tab === 'bolsa' ? 'bolsa' : tab)).classList.add('active');
      // reset period buttons
      document.querySelectorAll('.period-btn').forEach(b => b.classList.remove('active'));
      document.querySelector('.periods .period-btn[data-period="dia"]').classList.add('active');
      activePeriod = 'dia';
    }

    function cambiarTab(tab) {
      if (activeTab === tab) return;
      // set activeTab
      activeTab = tab;
      // highlight tab buttons
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
      // volver a dibujar gráfico con el mismo elemento y nuevo periodo
      if (activeEntity) mostrarGrafico(activeEntity);
    }

    // selecciona entidad (acción / bolsa / ibis)
    function seleccionarEntidad(name) {
      activeEntity = name;
      // activar botón
      document.querySelectorAll('.entity-btn').forEach(b => {
        b.classList.toggle('active', b.textContent === name);
      });
      mostrarGrafico(name);
      actualizarPanelInfo(name);
    }

    // Generar labels según periodo
    function generarLabels(periodo) {
      if (periodo === 'dia') {
        // últimos 30 días: mostrar dígitos 1..30 (o fechas relativas)
        const labels = [];
        for (let i = 30; i >= 1; i--) labels.push(`${i}d`);
        return labels;
      } else if (periodo === 'mes') {
        // 12 meses
        const months = ["Ene","Feb","Mar","Abr","May","Jun","Jul","Ago","Sep","Oct","Nov","Dic"];
        return months.slice(0,12);
      } else {
        // años (5)
        const currentYear = new Date().getFullYear();
        const years = [];
        for (let i = 4; i >= 0; i--) years.push(String(currentYear - i));
        return years;
      }
    }

    function obtenerDatos(entityName) {
      if (activeTab === 'acciones') {
        return datosSimulados.acciones[entityName][activePeriod];
      } else if (activeTab === 'bolsa') {
        return datosSimulados.bolsas[entityName][activePeriod];
      } else {
        return datosSimulados.ibises[entityName][activePeriod];
      }
    }

    function mostrarGrafico(entityName) {
      const ctx = document.getElementById('accionesChart').getContext('2d');
      const data = obtenerDatos(entityName);
      const labels = generarLabels(activePeriod);
      // si lengths mismatch (mes/anio) intentar ajustar:
      let displayData = data.slice();
      // Ajustar longitudes si es necesario (ej. meses 12, años 5, dia 30)
      const needed = labels.length;
      if (displayData.length > needed) displayData = displayData.slice(-needed);
      while (displayData.length < needed) {
        // pad replicando último valor
        displayData.unshift(displayData[0] || 0);
      }

      // colores por tendencia
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
                  // etiqueta bonita según pestaña
                  const val = context.formattedValue;
                  if (activeTab === 'ibises') return `Métrica: ${val}`;
                  return `Valor: ${val}`;
                }
              }
            }
          }
        }
      });
    }

    // Actualizar la sección derecha (descripcion / inversión) segun tab
    function actualizarPanelInfo(name) {
      const infoDiv = document.getElementById("companyInfo");
      // Para acciones y bolsas mostramos descripción e inversión
      if (activeTab === 'acciones' || activeTab === 'bolsa') {
        // datos 'ficticios' de descripción (puedes modificar textos)
        const descripcion = activeTab === 'acciones'
          ? `${name} es una empresa listada en mercados internacionales. Datos y métricas son simuladas.` 
          : `${name} es un mercado/índice global. Datos y métricas son simuladas.`;

        // Asignar capitalización/empleados/sector ficticios
        const sector = activeTab === 'acciones' ? "Tecnología / Servicios" : "Índice de Mercado";
        const capitalizacion = `$${(Math.random()*200 + 10).toFixed(1)}B`;
        const empleados = `${Math.floor(Math.random()*90000 + 1000).toLocaleString()}`;
        const mercado = activeTab === 'acciones' ? "NASDAQ / NYSE / BMV" : name;

        infoDiv.innerHTML = `
          <h2>${name}</h2>
          <p>${descripcion}</p>
          <div class="data-row"><span>Sector:</span><span>${sector}</span></div>
          <div class="data-row"><span>Capitalización:</span><span>${capitalizacion}</span></div>
          <div class="data-row"><span>Empleados:</span><span>${empleados}</span></div>
          <div class="data-row"><span>Mercado:</span><span>${mercado}</span></div>
          <div class="inversion-section">
            <label for="montoInversion">Monto a invertir en USD:</label>
            <input type="number" id="montoInversion" min="0" step="0.01" placeholder="Ejemplo: 1000" />
            <button onclick="calcularInversion()">Calcular Valor Actual</button>
            <div class="resultado-inversion" id="resultadoInversion"></div>
          </div>
        `;
      } else {
        // Ibises: mostrar métrica tipo "métrica de acciones de la empresa"
        const descripcion = `${name} — Vista de métrica (simulada) por Día / Mes / Año — representamos una métrica similar a 'valor' de mercado (opción C).`;
        // tomar último dato del periodo seleccionado
        const ultimo = obtenerDatos(name).slice(-1)[0];
        infoDiv.innerHTML = `
          <h2>${name}</h2>
          <p>${descripcion}</p>
          <div class="data-row"><span>Métrica actual:</span><span>${ultimo}</span></div>
          <div class="data-row"><span>Periodo:</span><span>${activePeriod}</span></div>
          <div style="margin-top:10px;font-size:14px;color:#666">Nota: Datos simulados (serie aleatoria)</div>
        `;
      }
    }

    // Calcular inversión (funciona para acciones y bolsas; ibises no tiene inversión)
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

      // Precio actual = último dato del array
      const precio = obtenerDatos(activeEntity).slice(-1)[0];
      const valorActual = monto * precio;
      resultado.textContent = `Con una inversión de $${monto.toFixed(2)} USD, el valor actual es $${valorActual.toFixed(2)} USD. (Precio base: ${precio})`;
      resultado.style.color = "#333";
    }

    // -------- Valores de monedas (simulados) y conversor --------
    let valoresMonedas = {
      MXN: 20.00,
      USD: 1.00,
      EUR: 0.92
    };

    function cargarValoresMonedas() {
      document.getElementById("valorMXN").querySelector('strong').textContent = `$${valoresMonedas.MXN.toFixed(2)}`;
      document.getElementById("valorUSD").querySelector('strong').textContent = `$${valoresMonedas.USD.toFixed(2)}`;
      document.getElementById("valorEUR").querySelector('strong').textContent = `${valoresMonedas.EUR.toFixed(2)}`;
    }

    function convertirMoneda() {
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

      // Convertir cantidad de origen a USD (base)
      let cantidadEnUSD;
      if (origen === "USD") cantidadEnUSD = cantidad;
      else cantidadEnUSD = cantidad / valoresMonedas[origen];

      // Convertir de USD a destino
      const cantidadDestino = cantidadEnUSD * valoresMonedas[destino];
      resultadoDiv.textContent = `${cantidad.toFixed(2)} ${origen} = ${cantidadDestino.toFixed(2)} ${destino}`;
      resultadoDiv.style.color = "#333";
    }

    // -------- Inicialización automática si el usuario ya está "logueado" (opcional) --------
    // (No forzamos login automático; el login debe ser realizado por el usuario.)

    // Nota: si deseas recargar datos simulados en cualquier momento:
    // generarTodosLosDatos(); seleccionarEntidad(getEntitiesForTab(activeTab)[0]);

  </script>
</body>
</html>
