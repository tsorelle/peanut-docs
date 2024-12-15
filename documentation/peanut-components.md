# Peanut UI Components
Notes from SCYM project may apply to or be adapted in the FMA project.

## Component types

We use "component" here to mean any Typescript/HTML code that can be reused across peanut ViewModel/Views.  They include:
- Knockout Components
- Observable container classes
- Model/View Patterns

### Knockout Components
Based on Knockout js component model. Like a viewmodel, has a view model class match with an HTML templated.  Peanut has special support for dynamic loading of thes components.

Components models are stored in a 'components' and named by convention:
````
componentName + 'Component.ts
e.g. personsSelectorComponent.ts
````
Templates are store in a 'templates' directory parallel to the components directory and named by convention:
````
componentName + '.html'
````
Here's a template for the component viewmodel:
```typescript
/// <reference path='../../typings/knockout/knockout.d.ts' />
namespace Peanut {
    export class componentnameComponent {
        // observables

        // variables.


        // include constructor if any params used
        constructor(params: any) {
            let me = this;

            if (!params) {
                console.error('componentnameComponent: Params not defined in translateComponent');
                return;
            }
            // initialize observavles and variables from params
            /* Example:
                if (!params.parameterName) {
                    console.error('componentnameComponent: Parameter "parameterName" is required');
                    return;
                }
            */

        }
    }
}
```
Component templates are an ordinary HTML fragment with data bindings to the component view model.

#### Initialization
Components are registered or loaded in the ViewModel.init() function using the application.registerComponent method. 
```typescript
me.application.registerComponents('@pnut/modal-confirm', () => {
    
});
```
Sometimes the view model needs some control over the component, for instance to load or clear data.  In this case
late binding can be used so that the view model may create the component vm and keep the reference.
In this technique, the application.loadComponent() method is used to reference the component source and
templalte then registerComponent is called passign in an instance of the model.
```typescript
me.application.loadComponents('tests/message-constructor', () => {
    let cvm = new messageConstructorComponent('Smoke Test Buttons:');
    me.application.registerComponent('tests/message-constructor', cvm, () => {
        me.bindDefaultSection();
        successFunction();
    });
});

```
### Comunication with ViewModel
The component can communicate with the owning ViewModel using one or more of these techniques. 
1. Pass a reference to the view model using a parameter in the markup with the value 'self'.  Often used to provide a translator object.
as in the case of translateComponent.
2. Pass a reverence to a method on the view model. See pagerComponent for an example.
3. Use the constructor in the case of a late bound component like 'personSelectorComponent'.

####Markup examples:
```html
<translate params="code:'form-legend',translator:self"></translate>
<pager params='click:onPagerClick,page:currentPage,max:maxPages,waiter:refreshing,owner:self'></pager>
```

#### ViewModel event handlers

The handle event method is typically used for communication between objects such as knockout components.  An evant handler 
method is defined in the interface IEventHandle (peanut.d.ts)
```typescript
    export interface IEventSubscriber {
        handleEvent : (eventName: string, data?: any) => void;
    }
```

ViewModelBase implements IEventSubscriber with an empty method, so overiding is optional and calls to handleEvent
have not effect if not overriden in the concrete view model class.

A reference to the view model may be passed using the constructor or in the markup, and implemented in view model like this:

```typescript
handleEvent = (eventName:string, data?:any)=> {
    let me = this;
    switch (eventName) {
        case 'person-selected' :
            me.newTerm(<INameValuePair>data.person);
            break;

        case 'person-search-cancelled' :
            // alert('Search cancelled.');
            break;
        case 'test' :
            alert('event test');
            break;
    }
};
```

```typescript
selectPerson = (personItem:INameValuePair)=> {
    var me = this;
    me.hide();
    if (me.owner) {
        me.owner.handleEvent('person-selected',
            {person: personItem, modalId: me.
              Id()}
        )
    }
};
```


### Observable container classes
These classes encapsulate a set of observables and may be used in different view models. Implementation of the view is done in the specific ViewModel/View that hosts the instance of this class.

#### Usages
Often used for forms.

Class example:
````
/pnut/packages/qnut-directory/js/PersonObservable.ts
````
Usage example:
````
/pnut/packages/qnut-directory/vm/UserProfileViewModel.ts
````

Or used for special ui components.
Class example:
````
/pnut/js/searchListObservable.ts
````

Usage example:
````
/pnut/packages/qnut-directory/js/DirectoryEntities.ts
````
###Model/View Patterns
These are simply code fragments that may be extracted from an existing ViewModel and View and adapted for use elsewhere.

They may be suitable for later refactoring to a component or reusable class.

## selectListObservable 
Type: knockout component

Encapsulates select list handling for INameValue[]
#### Initialize
In init() function 
````typescript
me.application.loadResources([
'@pnut/selectListObservable'
], () => {
  
});
````
#### Constructor
````typescript
me.taskEditForm.intervalType= new Peanut.selectListObservable(
    // handler - null if no handler
	me.onIntervalTypeChange,  
	// selection array
	[
          {name: 'On demand', id: 1},
          {name: 'Regular interval', id: 2},
          {name: 'Weeky', id: 3},
          {name: 'Daily', id: 4},
          {name: 'Fixed time', id: 5}
        ],
	// initial default value 
    1 );
````
#### Handler
````typescript
        onIntervalTypeChange = (type: ILookupItem) => {
            let me = this;
            me.taskEditForm.time('');
        };
````

HTML in view example
```html
<label for="document-types">Document type</label>
<select class="form-control form-select"  id="document-types"
   data-bind = "options: documentType.options, optionsText:'Name', value:documentType.selected"
></select>
```

for selectlist with handlers you need to call subscrible and unsubscribe
````typescript
this.taskEditForm.intervalType.unsubscribe();
if (value) {
	this.taskEditForm.intervalType.setValue(value);
}
else {
	this.taskEditForm.intervalType.setDefaultValue();
}
this.taskEditForm.intervalType.subscribe();
````
If list is of type ILookupItem[] use assignLookupList:
```typescript
me.sessiontypeSelector.assignLookupList(response.sessionTypes);
```
# Multiselect Component
Type: knockout component

Supports user input of multiple associated values to an "array" field.

**Source:**: /knockout_view/pnut/components/multiSelectComponent.ts
**Template:**: /knockout_view/pnut/templates/multiSelect.html
**Example:** /application/peanut/tests/vm/TestPageViewModel.ts and /view/TestPage.html

### Markup Example

````html
<multi-select params="items:itemList, selected:selectedItems,sort:'code',
    translator:self, label:'Multiselect', caption:'Select something...'">
</multi-select>
````
### Parameters
- items: ***Required.***  : KnockoutObservableArray of ILookupItem;
- selected:  ***Required.*** : KnockoutObservableArray of ILookupItem;
- translator: A host class to perform translations.  Usually a view model
- label: May be literal, or translator code.  Default blank.
- caption: May be literal, or translator code. Default 'Please select...'
- sort: Value for sorting options, Default: 'name'

### Data
Data is in the form of ILookupItem[]
```typescript
    export interface ILookupItem {
        id : any;
        code: string;
        name: string;
        description : string;
    }
```
### Dependencies and Initialization
Initialized from a Peanut ViewModel init() function.
```typescript
// register the component
me.application.registerComponents('@pnut/multi-select', () => {
        
});
```
Lodash dependencies remove in SCYM version. 1/22/2022.  Older versions require lodash library to be loaded.
```typescript
me.application.loadResources(['@lib:lodash'], () => {
    
});
```
#searchListObservable
Type: Observable container class.

A class for supporting presentation of a UI component allowing a pageable selection list based on a query selection result.

Source code:
```
/pnut/js/searchListObservable.ts
```
##Examples
Usage example, within a Knockout component:
````
/pnut/packages/qnut-directory/components/personSelectorComponent.ts
````
Usage within a ViewModel:
````
/pnut/packages/qnut-directory/vm/DirectoryViewModel.ts
````
## Initialization
```typescript
           me.application.loadResources([
                    '@pnut/ViewModelHelpers',
                    '@pnut/editPanel',
                    '@pnut/searchListObservable',
                ], () => {
               
                });

```
##Constructor 
###Example:
```typescript
me.personsList = new Peanut.searchListObservable(2, 12);
```
####Parameters:
- columnCount: Number of columns used to display result links
- maxInColumn: Maximum links in each column
#### Markup example

Note references to personList methods and observables. Also the use of $parent to reference the owning view model.

```html
 <div id="findPersonResults"  class="row found well" data-bind="visible:personsList.selectionCount">
    <div id="persons-found-header" class="row">
        <div class="col-md-12">
            <translate params="code:'label-found',translator:self"></translate>: <span data-bind="text: personsList.selectionCount"></span> <translate params="code:'dir-person-entity-plural',translator:self"></translate>.
        </div>

    </div>
    <div id="persons-found-nav" class="row">
        <div class="col-md-6" style="padding-right: 3px" data-bind="visible:personsList.hasPrevious">
            <a href="#"  data-bind="click:personsList.previousPage"><span class="fa fa-backward"></span> <translate params="code:'nav-previous',translator:self"></translate></a>
        </div>
        <div class="col-md-6" style="padding-right: 3px" data-bind="visible:personsList.hasMore">
            <a href="#" data-bind="click:personsList.nextPage"><translate params="code:'nav-more',translator:self"></translate>&nbsp;<span class="fa fa-forward"></span></a>
        </div>
    </div> <!-- end found header -->

    <div class="row" id="persons-found-list">
        <div class="col-xs-12 col-sm-6 col-md-6 col-lg-6" data-bind="foreach: personsList.selectionList[1]">
            <div class="link-list-item">
                <a href="#" data-bind="click:$parent.addPersonToAddress"><span data-bind="text: Name"></span></a>
            </div>
        </div>
        <div class="col-xs-12 col-sm-6 col-md-6 col-lg-6" data-bind="foreach: personsList.selectionList[2]">
            <div class="link-list-item">
                <a href="#" data-bind="click:$parent.addPersonToAddress"><span data-bind="text: Name"></span></a>
            </div>
        </div>
    </div> <!-- end found list -->

</div>

```
#### Methods
These methods and observables are typically used in the owning view model:
- setList(list : INameValuePair[]) - assign the search result list
- reset() - clear the search result list
- searchValue :  ko.observable(''); - extract the selected value (selectedItem.Value)

##Incremental Search Select Component

Type: Model/View Pattern

#### Example:
Extracted from:
````
/pnut/packages/qnut-directory/view/Directory.html
````
This example present a searchable selection list is presented in order for the user to select multiple affiliation values.

```html
<div class="form-group">
    <div>
        <span style="font-weight: bold"><translate params="code:'dir-label-organization',translator:self"></translate>: </span>
    </div>
    <div class="input-group" style="margin-bottom: 10px">
        <input class="form-control" id="orgsearch" type="search" name="search" placeholder="Select or search for an organization"
               data-bind="value: personForm.orgSearchValue, valueUpdate: 'keyup'" autocomplete="off" />
        <span class="input-group-btn"><button class="btn btn-default btn-outline-secondary" type="button" data-bind="click:personForm.onShowOrgList">
			<i data-bind="visible:personForm.orgListVisible" class="fa fa-caret-up"></i>
			<i data-bind="visible:!personForm.orgListVisible()"class="fa fa-caret-down"></i>
		</button></span>
    </div>
    <div style="position:absolute; z-index: 2; clear: both;background-color: white; max-height: 25ex; overflow: scroll; border: 1px solid lightgrey; padding: 10px"
         data-bind="visible:personForm.orgListVisible">
        <table class="table table-bordered" >
            <tbody data-bind="foreach:personForm.orgLookupList">
            <tr>
                <td data-bind="text:name"></td>
                <td><a href="#" data-bind="click:$parent.personForm.onOrgSelect">
                    <translate params="code:'label-select',translator:$parent.self"></translate></a> </td>
            </tr>
            </tbody>
        </table>
    </div>
</div>

```
Note the data-bind prameters to the input control:
````html
data-bind="value: personForm.orgSearchValue, valueUpdate: 'keyup'" autocomplete="off"
````
The view model code is in the observable container class  personObservable:
````
/pnut/packages/qnut-directory/js/PersonObservable.ts
````
Here the orgSearchValue is subscribed to a method that filters the list, orgLookupList, referenced in the table below.
```typescript
onOrgSearchChange = (value: string) => {
	let me = this;
	if (value) {
		me.selectedOrganization(null);
		me.orgLookupList([]);
		value = value.toLowerCase();
		let newlist = me.organizations.filter((org: Peanut.ILookupItem) => {
			return (org.name.toLowerCase().indexOf(value) >= 0);
		});
		me.orgLookupList(newlist);
		me.orgListVisible(newlist.length > 0);
	}
	else {
		me.orgLookupList(me.organizations);
	}
};
```
The "valueUpdate: 'keyup'" binding causes orgSearchValue() to be updated as the user types, thus triggering 
the onOrgSearchChange() method to refilter the list on every key stroke.

Also note the styles on the DIV element containing the table which position the selection list as a drop-down.

# lookupEditComponent
This component allows the a data management page for any entity that supports the ILookupItem interface.

##Markup example

##Parameters
Required:
- items: observable array
- onUpdate:  (item: ILookupItem) => void;

Optional:
- name: string - item name
- plural: string - plural version of name if needed
- canEdit: observable<boolean> or 'yes' - defaults to false
- translator: self - not supported at this time.

##Implementation
The view contains only the component tag and parameters.

Example:
```html
<div id="quarterlymeeting-load-message"><span class="fa fa-spinner fa-pulse fa-2x" style="color:lightgrey"></span></div>
<div id="quarterlymeeting-view-container" class="row" style="display: none">
    <lookup-edit params="
        items:quarterlies,
        onUpdate:updateQuarterly,
        name:'quarterly',
        plural:'quarterlies',
        canEdit:userCanEdit">
    </lookup-edit>
</div>

```
The View model resposibilities are:
1) Call the 


#To Do - Documentation needed

- Controller objects
  - listPageLoader
  - multiSelectObservable
  - searchListObservable
  - selectListObservable
