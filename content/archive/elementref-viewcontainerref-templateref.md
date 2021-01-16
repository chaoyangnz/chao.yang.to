---
title: 'ElementRef vs. ViewContainerRef vs. TemplateRef'
toc: true
date: "2016-12-27T17:11:25+00:00"
---


In Angular2, there are many *Ref class: `ElementRef`, `ViewContainerRef`, `TemplateRef`.

What do they means?

1. Nearly every components (Components, Attribute Directives, Structural Directives) have ViewContainerRef, which is the host of its view.

ViewContainerRef has two important methods:
`createEmbeddedView(templateRef: TemplateRef<C>, context?: C, index?: number) : EmbeddedViewRef<C>`
This method is to create a view from a HTML template.
`createComponent(componentFactory: ComponentFactory<C>, index?: number, injector?: Injector, projectableNodes?: any[][]) : ComponentRef<C>`
This method is to initialize a component and insert its host view to the view container.

1. From ViewContainerRef, we can get its ElementRef (further access its native DOM element).
2. For structural directives, since they are implemented through HTML template element, so it can get a TemplateRef.

> ViewContainerRef is a DOM element (container) where I can put your newly component as a **sibling** to this element. This may be counter-intuitive.


```typescript
// Component
import {AfterContentInit, Component, ElementRef, ViewContainerRef} from '@angular/core';

@Component({
	selector: 'app',
	template: `
  <h1>My App</h1>
  <pre style="background: #eee; padding: 1rem; border-radius: 3px; overflow: auto;"> 
    <code>{{ node }}</code>
  </pre>
`
})
export class App implements AfterContentInit {
  node: string;
  
  constructor(private viewContainerRef: ViewContainerRef) {
  }
  
  ngAfterContentInit() {
    let elementRef = this.viewContainerRef.element;
    const tmp = document.createElement('div');
    const el = elementRef.nativeElement.cloneNode(true);
    
    console.log(elementRef)
    console.log(this.viewContainerRef)
    
    tmp.appendChild(el);
    this.node = tmp.innerHTML;
  }
  
}
```




```typescript
// Attribute Directive
import {Directive, ViewContainerRef, ElementRef, Renderer} from 'angular2/core';

@Directive({
  selector: '[my-outline]',
  host: {
    '(mouseenter)': 'onMouseEnter()',
    '(mouseleave)': 'onMouseLeave()'
  }
})
export class MyOutline {
  constructor(private viewContainerRef:ViewContainerRef, private _element: ElementRef, private _renderer:Renderer) { }
  onMouseEnter() { this._outlineToggle(true); }
  onMouseLeave() { this._outlineToggle(false); }
  
  private _outlineToggle() {
    console.log(this.viewContainerRef)
    this._renderer.setElementStyle(this._element.nativeElement, 'border', 'solid red 1px' );
  }
}
```




```typescript
// Structural Directive
import {Directive, Input} from 'angular2/core';
import {TemplateRef, ViewContainerRef} from 'angular2/core';

@Directive({ selector: '[repeatMe]' })
export class RepeatMe {
  constructor(
    private _templateRef: TemplateRef,
    private _viewContainer: ViewContainerRef
    ) {}
  
  @Input() set repeatMe(count: int) {
    for (var i = 0; i < count; i++) {
      this._viewContainer.createEmbeddedView(this._templateRef);
    }
   }
}
```


In Angular2, the View is refered to the template of a component defined in the @Component decorator.

There are another view query docorators in Angualar.


```typescript
import { Component, ViewChild } from '@angular/core';
import { UserProfile } from '../user-profile';

@Component({
  template: '<user-profile (click)="update()"></user-profile>',
})
export class MasterPage {
  // ViewChild takes a class type or a reference name string.
  // Here we are using the type
  @ViewChild(UserProfile) userProfile: UserProfile

  constructor() { }

  ngAfterViewInit() {
    // After the view is initialized, this.userProfile will be available
    this.update();
  }

  update() {
    this.userProfile.sendData();
  }
}
```


Here we use `@ViewChild` to query the component, since the view is associated with a component.


