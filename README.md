# Sesión 26: ASP.NET y C#

## Fecha: 17/06/2025

## Objetivos de la Sesión

- Introducción a ASP.NET y C#

## Temas Cubiertos

1. **Fundamentos de C#**
   - Implementando el Patrón Repositorio con EF Core
   - Aplicaciones de consola más complejas
   - Introducción a aplicaciones de escritorio
   - Buenas prácticas y principios SOLID
   - Pruebas unitarias con xUnit

## Repositorio: SkyCast
Visita mi Repositorio SkyCast en [SkyCast](https://github.com/LizzyMr/Academia-Mega-SkyCast)

## Ejercicios Realizados

### Ejercicio 26: SkyCast - Release

Program.cs
```cs
using Microsoft.AspNetCore.Components.Web;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using SkyCast;
using SkyCast.Services;

var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("#app");
builder.RootComponents.Add<HeadOutlet>("head::after");

builder.Services.AddScoped<WeatherService>();
builder.Services.AddHttpClient<WeatherService>();

builder.Services.AddScoped(sp => new HttpClient { BaseAddress = new Uri(builder.HostEnvironment.BaseAddress) });

var envKey = builder.Configuration["OPENWEATHER_KEY"];

await builder.Build().RunAsync();
```

Home.razor
```cs
@page "/"                     
@using SkyCast.Services       
@inject WeatherService Api    

<h1 class="text-3xl font-bold mb-4">Pronóstico</h1>

<!-- Sección de entrada para escribir la ciudad y botón para buscar -->
<div class="mb-4 flex gap-2">
    <input 
        @bind="city" 
        placeholder="Ciudad..."
        class="border rounded px-2 py-1 flex-1" />
    <button 
        class="bg-blue-600 text-white px-4 rounded" 
        @onclick="Search"> 
        Buscar 
    </button>
</div>

<!-- Mostrar mensajes según el estado de la carga -->
@if (loading)
{
    <p>Cargando...</p>    @* Indicador de carga mientras se consulta la API *@
}
else if (error != null)
{
    <p class="text-red-500">@error</p>  @* Muestra mensaje de error si ocurrió alguno *@
}
else if (data != null)
{
    <!-- Mostrar resultados del clima si se obtuvo información correctamente -->
    <div class="p-6 rounded shadow bg-white max-w-sm">
        <h2 class="text-xl font-semibold">@data.Name</h2>
        <p class="text-6xl">@data.Main.Temp.ToString("0") °C</p>
        <p class="capitalize">@data.Weather.First().Description</p>
        <p class="text-sm text-gray-500">Humedad: @data.Main.Humidity %</p>
    </div>
}

@code {
    // Ciudad inicial por defecto
    string city = "Guadalajara";

    // Indica si se está cargando la información
    bool loading;

    // Almacena errores si ocurren durante la búsqueda
    string? error;

    // Almacena los datos del clima obtenidos desde el API
    WeatherDto? data;

    /// <summary>
    /// Método que realiza la búsqueda del clima según el valor de 'city'
    /// </summary>
    async Task Search()
    {
        loading = true;    // Muestra indicador de carga
        error = null;      // Limpia errores previos

        try
        {
            data = await Api.GetByCityAsync(city);  // Llama al servicio
            if (data == null)
                error = "Ciudad no encontrada";      // Valida si no se devolvieron datos
        }
        catch (Exception ex)
        {
            error = ex.Message;  // Captura errores (por ejemplo, si falla la conexión)
        }

        loading = false;  // Oculta indicador de carga
    }

    /// <summary>
    /// Ejecuta la búsqueda automáticamente al cargar la página
    /// </summary>
    protected override async Task OnInitializedAsync()
        => await Search();
}
```

WeatherService.cs
```cs
using System.Net.Http.Json;
using Microsoft.Extensions.Configuration;

namespace SkyCast.Services;

/// <summary>
/// Servicio para obtener datos meteorológicos desde la API de OpenWeather.
/// </summary>
public class WeatherService
{
    private readonly HttpClient _http; // Cliente HTTP para hacer peticiones a la API
    private readonly string _key; // Clave de API para autenticarse con OpenWeather
    private const string baseURL = "https://api.openweathermap.org/data/2.5/"; // URL base de la API

    /// <summary>
    /// Inicializa una nueva instancia del <see cref="WeatherService"/>.
    /// </summary>
    /// <param name="http">Instancia de <see cref="HttpClient"/> utilizada para realizar las solicitudes HTTP.</param>
    /// <param name="config">Configuración de la aplicación que contiene la clave de API de OpenWeather.</param>
    public WeatherService(HttpClient http, IConfiguration config)
    {
        _http = http;
        _key = config["OPENWEATHER_KEY"] ?? ""; // Lee la clave desde la configuración
    }

    /// <summary>
    /// Obtiene los datos del clima para una ciudad específica.
    /// </summary>
    /// <param name="city">Nombre de la ciudad.</param>
    /// <returns>Un objeto <see cref="WeatherDto"/> con los datos del clima, o <c>null</c> si no se encuentra la ciudad.</returns>
    public async Task<WeatherDto?> GetByCityAsync(string city)
    {
        // Construye la URL con el nombre de la ciudad, en unidades métricas y lenguaje español
        var url = $"{baseURL}/weather?q={Uri.EscapeDataString(city)}&units=metric&appid={_key}&lang=es";

        // Hace la petición HTTP y deserializa automáticamente la respuesta JSON a WeatherDto
        return await _http.GetFromJsonAsync<WeatherDto>(url);
    }
}
```

skycast-app.yml
```yml
name: Build & Deploy BlazorApp

on:
  push: 
    branches: ["main"]

env:
  DOTNET_VERSION: '8.0.x'
  NODE_VERSION: '22.X'

jobs:
  build:
    runs-on: ubuntu-lastest

    steps: 
      # 1. Descargar el código
      - uses: actions/checkout@v4

      # 2. Instalar .NET

      # 3. Instalar Paquetes Nuget

      # 4. Instalar Node

      # 5. Restore and Publish

      # 6. Crear Artifact

      # 7. Despliegue
```

.gitignore
```git
SkyCast/bin
SkyCast/obj
```
## Desafíos Encontrados

- **Sin Impedimentos:** Para esta actividad no tuve ningún problema.


## Recursos Adicionales

- Curso Completo de Programación C# en .Net y muchísimo más
- Programación C# con Visual Studio Code 2021 de 0 a EXPERTO
- Aprende a programar desde cero con C#, Microsoft .NET y WPF
- Clean Architecture en C# .NET, un curso basado en conceptos
- Pruebas unitarias con xUnit en .NET

## Próximos Pasos

- Continuar los avances en cursos de Udemy. 
- Practicar los ejercicios realizados en la sesión.

## Reflexiones Personales

Fue una práctica muy interesante y entretenida.

---

*Entregable correspondiente a la Semana 13 del Módulo 3: ASP.NET y C#*