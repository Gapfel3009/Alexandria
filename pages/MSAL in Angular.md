- Beispiel: Angular 17 Standalone aus Skills Portal
- MSAL Konfigurieren:
	- Auth-Config.ts
		- ```TypeScript
		  import { MsalInterceptorConfiguration } from '@azure/msal-angular';
		  import { Configuration, PublicClientApplication, InteractionType } from '@azure/msal-browser';
		  //Konfigurierung von MSAL
		  export const msalConfig: Configuration = {
		    //Die daten aus deiner App Registrierung
		      auth:{
		          clientId: 'ClientId',
		          redirectUri: 'RedirectUrl (localhost)',
		          authority: 'https://login.microsoft.com/TenantId',
		      },
		      cache:{
		          cacheLocation: 'localStorage',
		          storeAuthStateInCookie: false,
		      },
		  }
		  export const loginRequest = {
		      scopes: [Scopes],
		    };
		  
		    export function MSALInstanceFactory(){
		      return new PublicClientApplication(msalConfig);
		    }
		  
		    export function MSALInterceptorConfigFactory(): MsalInterceptorConfiguration{
		      const protectedResourceMap = new Map<string, Array<string>>();
		      protectedResourceMap.set('https://graph.microsoft.com/v1/*', ['User.Read.All']);
		      return{
		          interactionType: InteractionType.Popup,
		          protectedResourceMap
		      };
		    }
		  ```
	- App-config.ts bestimmt genauer wie MSAL initialisiert werden soll
		- ```TypeScript
		  import { ApplicationConfig } from '@angular/core';
		  import { provideRouter } from '@angular/router';
		  import { routes } from './app.routes';
		  import { HTTP_INTERCEPTORS, provideHttpClient} from '@angular/common/http';
		  import { MSALInstanceFactory, MSALInterceptorConfigFactory } from './auth/auth-config';
		  import { MsalService, MsalGuard, MsalInterceptor, MSAL_INSTANCE, MSAL_INTERCEPTOR_CONFIG, MsalBroadcastService } from '@azure/msal-angular';
		  
		  export const appConfig: ApplicationConfig = {
		      
		      providers: [provideRouter(routes),
		        provideHttpClient(),
		        MsalService,
		      {
		        provide: MSAL_INSTANCE,
		        useFactory: MSALInstanceFactory
		      },
		      MsalService,
		      MsalGuard,
		      MsalBroadcastService,
		    {
		        provide: MSAL_INTERCEPTOR_CONFIG,
		        useFactory: MSALInterceptorConfigFactory
		      },
		      {
		        provide: HTTP_INTERCEPTORS,
		        useClass: MsalInterceptor,
		        multi: true
		      },
		    ]
		  ```
	- auth.Service.ts
		- import { Injectable} from '@angular/core';
		  import { MsalService } from '@azure/msal-angular';
		  import { MSALInstanceFactory,} from './auth-config';
		  import {AuthenticationResult} from '@azure/msal-browser'
		  import {  Router } from '@angular/router';
		  @Injectable({
		    providedIn: 'root'
		  })
		  export class AuthService{
		    constructor(private authService: MsalService,
		      private router: Router
		    ) {
		      this.authService.instance = MSALInstanceFactory();
		      this.authService.instance.initialize();
		    }
		  
		    login() {
		      this.authService.loginPopup().subscribe((response: AuthenticationResult) => {
		        this.authService.instance.setActiveAccount(response.account);
		        this.router.navigate(['/mainsite']);
		      });
		    };
		  
		    logout() {
		     this.authService.logout()
		    }
		  
		    isLoggedIn() : boolean{
		      return this.authService.instance.getActiveAccount() != null;
		    }
		  }
	-
	- Auth-Config & App-Config konfigurieren und initialisieren MSAL. In Auth.Service.ts sind die Aufrufe und befehle zum anmelden, diese müssen mit Html richtig eingesetzt werden (also normale Funktionsaufrufe in HTML).
-