# modal-confirm component
## Example markup:
````html
<modal-confirm params="id:'confirm-save-modal',headerText: 'Please confirm SAVE', bodyText: confirmText, confirmClick: save"></modal-confirm>
<modal-confirm params="id:'confirm-delete-modal',headerText: 'Please confirm', bodyText: confirmText, confirmClick: confirm"></modal-confirm>
````
Text parameters (bodyText, headerText) can be either a string (in quotes) or an observable (no quotes)

## Example handling
````typescript
showSaveModal = () => {
jQuery("#confirm-save-modal").modal('show');
};

    save = () => {
       jQuery("#confirm-save-modal").modal('hide');
            alert('you saved');
     };
````
## Example initialization

init(applicationPath: string, successFunction?: () => void) {
me.application.initialize(applicationPath,
function() {
me.application.registerComponent('@pnut/modal-confirm', () => {
...
});

         }
     );
}

Additional Parameters:
case - use to alter the case of the translation:  'upper', 'lower' or 'ucfirst'
'ucfirst' uppercases the first letter only.
example:
<translate params="code:'mailbox-entity-plural',case:'ucfirst',translator:self"></translate>
