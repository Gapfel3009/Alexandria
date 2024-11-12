- Du hast [[Verbindung von ASP.Net Backends & Angular Frontend mittels NSwag]] durch gelesen oder hast eine Verbindung aufgebaut und möchtest nun testen ob es tatsächlich funktioniert hat dafür muss man den Gewählten Component ändern in unserem Beispiel App.Component.ts und app.config.ts
  App.Component.ts:
  ```TypeScript
  import { Component } from '@angular/core';
  import { RouterOutlet } from '@angular/router';
  
  @Component({
    selector: 'app-root',
    standalone: true,
    imports: [RouterOutlet],
    templateUrl: './app.component.html',
    styleUrl: './app.component.css'
  })
  export class AppComponent {
    title = 'SkillsPortal.Frontend';
  }
  //Der oben gezeigte Code ist der standart Code der automatisch generiert wird hier drunter ist der aktuellisierte CLient.ts ist der name des Outputs von NSwagStudio
  
  //^~^//
  
  import { Component } from '@angular/core';
  import { RouterOutlet } from '@angular/router';
  import { Client } from '../API/client';
  import { HttpClient, HttpHandler } from '@angular/common/http';
  
  @Component({
    selector: 'app-root',
    standalone: true,
    imports: [RouterOutlet],
    providers: [Client],
    templateUrl: './app.component.html',
    styleUrl: './app.component.css'
  })
  export class AppComponent {
    title = 'SkillsPortal.Frontend';
    constructor(
      private Apiclient: Client
    ){
      console.log("hi");
      Apiclient.getWeatherForecast().subscribe();
      console.log("tschau");
    }
  }
  ```
  Die app.config.ts Datei sieht wie Folgend aus:
  ```TypeScript
  import { ApplicationConfig } from '@angular/core';
  import { provideRouter } from '@angular/router';
  
  import { routes } from './app.routes';
  
  export const appConfig: ApplicationConfig = {
    providers: [provideRouter(routes)]
  };
  
  //
  
  import { ApplicationConfig } from '@angular/core';
  import { provideRouter } from '@angular/router';
  
  import { routes } from './app.routes';
  import { provideHttpClient } from '@angular/common/http';
  import { API_BASE_URL } from '../API/client';
  
  export const appConfig: ApplicationConfig = {
    providers: [provideRouter(routes),
      provideHttpClient(),
      {provide: API_BASE_URL, useFactory:()=> 'https://localhost:7174'}
    ]
  };
  ```
  Durch diesen code von den Component und Config Datein wird im Dev Tool des Browsers eine zwei Nachrichten in der Konsole ausgegeben. Erst ein "Hi" das zeigt dass der Constructor aufgerufen wurde dann wird die WeatherForecast Api aufgerufen dort kommt kein output in der Konsole an um zu sehen ob es vorbei ist kommt ein "Tschau" in der Konsole Zurück so kann man sehen ob Backend mit Frontend verbunden ist
-
-