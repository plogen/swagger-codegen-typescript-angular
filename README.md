# Resources:
A guide that helped me get Swagger Codegen to work:\
https://medium.com/sohoffice/the-api-first-strategy-use-swagger-to-generate-api-client-as-an-angular-library-66964ea43587

# swagger-codegen-cli
swagger-codegen-cli can be downloaded from:\
http://central.maven.org/maven2/io/swagger/codegen/v3/swagger-codegen-cli/ \
In below instructions i have downloaded swagger-codegen-cli-3.0.13.jar and placed it in to the Angular root folder. Feel free to place it anywhere but do the appropiare changes 

# Generate a Angular Library
Generate a Angular library template:
* cd to Angular project root folder.
* ng generate library phome-brewery-api

In the root folder the library was generated in projects/phome-brewery-api
* delete the src folder (template files that is not needed because we will generate/use swagger generated code later on

# Generate typescript-angular code with swagger-codegen-cli
* Start your API, in my case a .NET Core WebAPI with the service Swashbuckle.AspNetCore running on localhost
* cd to Angular project root folder
* run the following command in the Angular project root folder:\
java -jar swagger-codegen-cli-3.0.13.jar generate -i http://localhost:5000/api-docs/v1/openapi.json -l typescript-angular -o ./projects/phome-brewery-api/src
* Manualy modify ./projects/phome-brewery-api/ng-package.json\
from:\
"entryFile": "src/public-api.ts"\
to:\
"entryFile": "src/index.ts"\
		
* Do a build of phome-brewery-api to make it available\
	ng build phome-brewery-api
		
* Add the library to app.module.ts
```
import {ApiModule, BASE_PATH} from 'phome-brewery-api';

@NgModule({
	declarations: [
	],
	imports: [
	ApiModule
	],
	providers: [{ provide: BASE_PATH, useValue: 'http://localhost:5000' }],
	bootstrap: [AppComponent]
})
```
	
* Add below to all new Angular components. All services from the API has been exported as Injectables so each Service can be referenced to

```
import { Component, OnInit } from '@angular/core';
import {RecipesService} from 'swagger-client';

@Component({
	selector: 'app-active-recipe',
	templateUrl: './active-recipe.component.html',
	styleUrls: ['./active-recipe.component.sass']
})
export class ActiveRecipeComponent implements OnInit {

	constructor(private apiClient: RecipesService) { }

	ngOnInit() {

	this.apiClient.getRecipe('5dcc77b120cf3b5c28cdc033').subscribe((data)=>{
		console.log(data);
	});    

	}
}
```	
	
## Actions on API updates	
When updates have been done on phome-brewery-api
* run the following command in the Angular project root folder:\
* java -jar swagger-codegen-cli-3.0.13.jar generate -i http://localhost:5000/api-docs/v1/openapi.json -l typescript-angular -o ./projects/phome-brewery-api/src
* ng build phome-brewery-api
* ng serve