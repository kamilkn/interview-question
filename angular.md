# Angular

## Директивы
* Компоненты

* Структурные
- *ngIf, *ngFor, *ngSwitch, можем создвать свои

* Атрибутные
- [ngClass], [ngStyle], опять же можем создавать свои

## RxJS
https://rxru.fun/#/


## ng-template vs ng-content vs ng-container
`ng-template` это не DOM элемент, в отличии от ng-container. Это низкоуровневая абстракция, которая компилируется в анонимный компонент внутри компонента, технически это называется "embedded view".
  <div class="lessons-list" *ngIf="lessons else loading">
      ...
  </div>

  <ng-template #loading>
      <div>Loading...</div>
  </ng-template>

`ng-container` - для добавления *ngIf="" или других структурных директив и чтобы это не было видно в DOM
или для встривания ng-template
  <ng-container *ngTemplateOutlet="loading"></ng-container>


## trackBy
для оптимизации
As a result, Angular needs to remove all the DOM elements that associated with the data and create them again. That means a lot of DOM manipulations especially in a case of a big collection, and as we know, DOM manipulations are expensive.
```
setInterval( () => {
  this.list.length = 0;
  this.list.push({name: 'Gustavo'});
  this.list.push({name: 'Costa'});
}, 2000);

@Component({
  selector: 'my-app',
  template: `
   <li *ngFor="let item of list; trackBy:identify">{{item.name}}</li>
  `
})
export class App {
  list:[];

  identify(index, item){
     return item.name; 
  }
```

Renderer 2 - прослойка между реальным DOM и компонентом
лучше использовать его вместо прямого обращения
чтобы можно было спокойно подключить SSR и ничего не сломалось

## Change detection strategies (стратегии изменения)

### Default
При любом изменении для всех компонентов делает change

### OnPush
* компонент не будет обновляться/повторно отображаться при обновлении свойства родительского компонента

Только если:
- изменяется любое из его (только его) входных свойств @Input
- Внутри компонента Angular или его потомках вызываются события 
- Observable запускает событие
- Имеют место обновления переменной состояния в Angular.
- Ручной запуск обнаружения изменений detectChanges()
- ApplicationRef.tick()
- markForCheck() помечает компонент и всех его родителей, что они должны быть проверены в текущем или следующем цикле обнаружения изменений

ng.profiler.timeChangeDetection()
