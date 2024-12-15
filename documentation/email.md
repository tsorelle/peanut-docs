# EMail

## TPostOffice

### SendMessage from Us

```phpt
public static function SendMessageFromUs(
    $recipients, $subject, $bodyText, 
    $addressId=self::DefaultMailbox, 
    $contentType=TContentType::Html ) 
```
The $recipients can be a single instance or an array of strings or TEmailAddress objects.

String formats include:
1. Simple address: 'recipient@mailservice.com'
2. Name and address format 'Joe Smith <recipient@mailservice.com>'

Multiple addresses can be combined in a single string, seperated by ';'.

TEmailAddress obects can be created as:
```phpt
new TEmailAddress($address, $name);
```
The optional $addressId parameter is a mailbox code.



## Template Example
```phpt
$templateManager = new MailTemplateManager();
$template = $templateManager->getTemplateContent('SiteAnnouncement.html');

$recipient = new \Tops\mail\TEmailAddress($user->email,$user->fullname);
$bodyText = TTemplateManager::ReplaceContentTokens($template, [
    'fullname' => $user->fullname,
    'username' => $user->username,
    'pwd' => $user->pwd]);
    
\Tops\mail\TPostOffice::SendMessageFromUs(
    $recipient,
    'New Website for SCYM',
    $bodyText
);
```
### Template Search Method
TTemplateManager starts template search in: *'application/peanut/mail/templates'*

If not found: **packages/knockout_view/pnut/mail/templates**

Templates are organized in subdirectories by language code such as "en".
You may create an html version, txt version or both.

### Template Format
Templates contain tokens for replacement surrounded by double square brackets: [[token]].

Example:
```html
<h3>Greetings [[fullname]] from [[organizationname]]</h3>
<p>
    We have created an account for you on our web site. You can sign in here:
    <a href="[[loginlink]]"><strong>Sign in</strong></a>
</p>
<p>
    Your user name is:
    <strong>[[username]]</strong>
</p>
<p>
    Previously, you selected a password or we assigned one for you. If you don't know what it is, go here:<br>
    <a href="[[passwordlink]]"><strong>Recover your password.</strong></a>
</p>
<p>
    We are delighted to have you with us. If you need any help, contact our web support at:<br>
    <strong>[[supportaddress]]</strong>
</p>
```

