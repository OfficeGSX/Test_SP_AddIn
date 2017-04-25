---
title: Hervorheben von Inhalten und Erweitern der Funktionen in von SharePoint gehosteten SharePoint-Add-Ins mithilfe des Popupsteuerelements
ms.prod: SHAREPOINT
ms.assetid: bfa367bb-d2f5-4e3f-bf48-61b77f150f7d
---



# Hervorheben von Inhalten und Erweitern der Funktionen in von SharePoint gehosteten SharePoint-Add-Ins mithilfe des Popupsteuerelements
Das Popupsteuerelement von SharePoint ermöglicht eine flexible Interaktion mit dem Benutzer sowie die Präsentation der Funktionen Ihrer von SharePoint gehosteten App. Das Steuerelement kann auf vielfältige Weise konfiguriert werden, um es auf die UI Ihrer App zuzuschneiden. In diesem Artikel erfahren Sie, wie Sie dieses Steuerelement erstellen und Ihrer Seite hinzufügen sowie Darstellung und Verhalten des Steuerelements anpassen.Bei Suchvorgängen auf einer SharePoint 2013-Website sehen Sie das Popupsteuerelement in Aktion: Es erscheint immer dann, wenn Sie auf ein Suchergebnis zeigen. Abbildung 1 zeigt das Popup für ein einzelnes Suchergebnis sowie einige typische Elemente eines Inhaltssteuerelements: einen Titel, einige Informationen zum Element auf der Seite sowie Aktionen ( **Öffnen** und **Senden**), die für das Element ausgeführt werden können. In diesem Fall sind die Informationen und Aktionen zwar relativ simpel, Sie können jedoch bereits zwei Vorteile erkennen. Erstens: Sie können zusätzliche Informationen zu Elementen auf einer Seite anzeigen, wenn dies erforderlich ist. Und zweitens: Der Funktionsumfang der Seite lässt sich so auf elegante Weise erweitern. 
**Abbildung 1: Beispiel des Popupsteuerelements auf einer Seite mit SharePoint 2013-Suchergebnissen**








![Beispiel für das Popup-Steuerelement auf einer SharePoint 2013-Suchergebnisseite](images/S15_CalloutControlExample.png)












## Verfügbarmachen des Steuerelements für die HTML-Seite durch Einbeziehen der Datei "callout.js"
<a name="GettingStarted"> </a>

In diesem Beispiel wird mithilfe der Methode  `SP.SOD.executeFunc` sichergestellt, dass die Skriptdatei geladen wird, bevor Sie davon abhängigen Code ausführen können.




```

SP.SOD.executeFunc("callout.js", "Callout", function () {
    });
```

Die Funktion, die Sie an die Funktion  `SP.SOD.executeFunc` übergeben, enthält den Code, der ausgeführt werden soll, nachdem die Datei "callout.js" geladen wurde. Nach dem Laden dieser Dateien wird mithilfe des Objekts `CalloutManager` für jedes Seitenelement, dem ein Popupsteuerelement zugeordnet sein soll, ein Objekt vom Typ `Callout` erstellt. Bei `CalloutManager` handelt es sich um einen Singleton, mit dem Verweise auf sämtliche Objekte vom Typ `Callout` auf einer Seite innerhalb eines assoziativen Arrays gespeichert werden. Das Objekt `Callout` besitzt zwei erforderliche Elemente: `ID` und `launchPoint`. Das Element  `ID` ist der Schlüssel, der dem Objekt `Callout` in `CalloutManager` zugeordnet ist: `CalloutManager["value of the callout's ID member"]`. Das Element  `launchPoint` ist ein HTML-Seitenelement. Sie können beispielsweise ein Element vom Typ `div` auf Ihrer Seite erstellen oder abrufen und es als Element des Objekts `Callout` übergeben. Standardmäßig erscheint das Popupsteuerelement, wenn ein Benutzer auf das Element `launchPoint` klickt. Das folgende Beispiel zeigt, wie Sie das einfachste aller Popupsteuerelemente mit lediglich den beiden erforderlichen Elementen und einer Titelzeichenfolge erstellen:






```

var calloutPageElement = document.createElement("div");
var callout = CalloutManager.createNew({
   ID: "unique identifier",
   launchPoint: calloutPageElement,
   title: "callout title"
});

```

Dieses Popup wird mit einem Titel am oberen Rand des Steuerelements angezeigt, wenn ein Benutzer auf das Seitenelement klickt. Mithilfe der optionalen Elemente lassen sich Aspekte wie Darstellung, Verhalten, Position und Aktionen des Steuerelements auf vielfältige Weise und überaus wirkungsvoll anpassen. Das Popupsteuerelement verfügt auch über eine set-Methode, mit der Sie nach Erstellung einer Instanz des Steuerelements einen Wert für einen beliebigen Parameter festlegen können.






```

callout.set({openOptions:{event: "hover"}});
```

Sie können auch Werte für alle Popupelemente in einem Objekt vom Typ  `CalloutOptions` festlegen und das Objekt anschließend an die Methode `createNew` übergeben.






```
var calloutPageElement = document.createElement("div");
var calloutOptions = new CalloutOptions();
calloutOptions.ID = unique identifier;
calloutOptions.launchPoint = calloutPageElement;
calloutOptions.title = callout title;
var callout = CalloutManager.createNew(calloutOptions);
```


## So wird's gemacht: Anpassen der Darstellung des Popupsteuerelements
<a name="Appearance"> </a>

Die folgenden Elemente dienen zum Steuern der Popupdarstellung:





|**Element**|**Zweck**|**Gültige Werte (Standardwert in Fettformatierung)**|
|:-----|:-----|:-----|
|Title  <br/> |Dient zum Anzeigen eines Titels am oberen Rand des Steuerelements.  <br/> |Zeichenfolge, **NULL**, Zeichenfolge mit HTML <br/> |
|Content  <br/> |Dient zum Anzeigen von HTML innerhalb des Steuerelements, wenn kein Wert für das Element  `contentElement` vorhanden ist. <br/> |Zeichenfolge mit HTML, **NULL**, muss NULL sein, wenn `contentElement` einen Wert besitzt <br/> |
|contentElement  <br/> |Dient zum Anzeigen eines HTML-Elements innerhalb des Steuerelements, wenn kein Wert für das Element  `content` vorhanden ist. <br/> |beliebiges HTML-Element, **NULL**, muss NULL sein, wenn `content` einen Wert besitzt <br/> |
|contentWidth  <br/> |Dient zum Angeben der Breite (in Pixel) des Popuptextcontainers. Dieser Container besitzt einen Rand mit einer Breite von einem Pixel sowie einen Abstand mit einer Breite von 15 Pixeln an jeder Seite, wodurch die Breite des Steuerelements die angegebene Textbreite um 32 Pixel übersteigt. Die CSS-Eigenschaft  `overflow` des Steuerelements ist auf `hidden` festgelegt, sodass der Inhalt abgeschnitten wird, falls er nicht in die angegebene Breite passt. Falls Sie dieses Element für ein geöffnetes Popup festlegen, wird die Änderung umgehend wirksam. Bei den anderen Elementen ist dies nicht der Fall. <br/> |Beliebige Zahl zwischen 240 und 610, **350** (wodurch die Breite des Steuerelements standardmäßig 382 Pixel beträgt) <br/> |
|beakOrientation  <br/> |Dient zum Angeben der Ausrichtung der Spitze oder des Zeigers des Popupsteuerelements.  <br/> |**topBottom**, siehe Abbildung 2: **Abbildung 2: Position der Spitze des Popupsteuerelements bei Verwendung der Ausrichtung "topBottom"**!\[Wo die Spitze des Popup-Steuerelements nach oben/unten zeigt](images/SP15_CalloutTopBottom.png) **leftRight**, siehe Abbildung 3: **Abbildung 3: Position der Spitze des Popupsteuerelements bei Verwendung der Ausrichtung "leftRight"**!\[Wo die Spitze des Popup-Steuerelements nach links/rechts zeigt](images/SP15_CalloutLeftRight.png)|
 

## So wird's gemacht: Anpassen des Verhaltens des Popupsteuerelements
<a name="Behavior"> </a>

Die folgenden Elemente dienen zum Steuern des Popupverhaltens. Beginnen Sie mit dem wichtigen Element  `openOptions`, da Sie damit angeben können, wie das Steuerelement geöffnet und geschlossen werden soll, wenn der Benutzer auf der Seite damit interagiert.





|**Werte für das Element  `openOptions`**|**Zweck**|
|:-----|:-----|
|**{event: "click", closeCalloutOnBlur: true}** <br/> |Das Popup erscheint, wenn der Benutzer mit einer Maus auf das Element  `launchPoint` klickt, und wird geschlossen, wenn der Benutzer die Maus vom Element `launchPoint` wegbewegt. Da `event` den Wert `click` besitzt, lautet der Wert der Option `showCloseButton` standardmäßig **true** und kann nicht geändert werden. Hierbei handelt es sich um die standardmäßige Wertekombination. <br/> |
| `{event: "hover", showCloseButton: true}` <br/> |Das Popup erscheint, wenn der Benutzer mit einer Maus auf das Element  `launchPoint` zeigt, und wird geschlossen, wenn der Benutzer auf ein **X** in der rechten oberen Ecke des Steuerelements klickt. Da `event` den Wert `hover` besitzt, ist der Wert von `closeCalloutOnBlur` nicht anwendbar und kann auch nicht festgelegt werden. <br/> |
| `{event: "click", closeCalloutOnBlur: false}` <br/> |Das Popup erscheint, wenn der Benutzer mit einer Maus auf das Element  `launchPoint` zeigt, und wird nur geschlossen, wenn der Benutzer auf ein **X** in der rechten oberen Ecke des Steuerelements klickt. Da `event` den Wert `click` besitzt, lautet der Wert der Option `showClosebutton` standardmäßig **true** und kann nicht geändert werden. <br/> |
 
Im Anschluss finden Sie die anderen Elemente, die zum Steuern des Popupverhaltens festgelegt werden können:





|**Element**|**Zweck**|**Gültige Werte (Standardwert in Fettformatierung)**|
|:-----|:-----|:-----|
|onOpeningCallback  <br/> |Dient zum Ausführen von Aktionen, die vor dem Rendern des Popups auf der Seite ausgeführt werden müssen. Da das Objekt  `Callout` als Parameter an die angegebene Funktion übergeben werden muss, können Sie mithilfe dieses Elements Werte für Eigenschaften des Steuerelements festlegen, bevor das Steuerelement gerendert wird. Mit diesem Element können Sie auch asynchrone Aktionen starten, um dem Steuerelement Inhalte hinzuzufügen oder Inhalte des Steuerelements zu ändern. Für dieses Element kann nur einmal ein Wert festgelegt werden. <br/> | `function(callout /*=Callout*/) {...}`, **null** <br/> |
|onOpenedCallback  <br/> |Dient zum Ausführen von Aktionen, die ausgeführt werden müssen, nachdem das Popup gerendert und vollständig animiert wurde. Mit diesem Element können Sie beispielsweise das Dokumentobjektmodell (DOM) des Steuerelements ändern. Für dieses Element kann nur einmal ein Wert festgelegt werden.  <br/> | `function(callout /*=Callout*/) {...}`, **null** <br/> |
|onClosingCallback  <br/> |Dient zum Ausführen von Aktionen, die ausgeführt werden müssen, wenn das Steuerelement geschlossen wird, aber noch vollständig von der Seite entfernt wurde. Für dieses Element kann nur einmal ein Wert festgelegt werden.  <br/> | `function(callout /*=Callout*/) {...}`, **null** <br/> |
|onClosedCallback  <br/> |Dient zum Ausführen von Aktionen, die ausgeführt werden müssen, wenn das Steuerelement geschlossen und vollständig von der Seite entfernt wurde. Für dieses Element kann nur einmal ein Wert festgelegt werden.  <br/> | `function(callout /*=Callout*/) {...}`, **null** <br/> |
 

## So wird's gemacht: Verwenden der Methoden des Popupsteuerelements
<a name="CalloutMethods"> </a>

Die folgenden Methoden dienen zum Anpassen des Verhaltens des Popupsteuerelements:





|**Methode**|**Zweck**|**Gültige Parameterwerte**|
|:-----|:-----|:-----|
|set({member:value})  <br/> |Dient zum Festlegen von Werten für Elemente, nachdem eine Instanz des Steuerelements erstellt wurde.  <br/> |Ein Name-Wert-Paar zum Definieren eines Werts für ein beliebiges Element des Popupsteuerelements.  <br/> ```var callout = new Callout({openOptions:{event: "click"}});callout.set({openOptions:{event: "hover"}});```|
|getOrientation()  <br/> |Gibt ein Objekt vom Typ  `CalloutOrientation` zurück, das angibt, in welche Richtung das Popupsteuerelement zeigt. Dieses Objekt besitzt vier boolesche Elemente: `up`,  `down`,  `left` und `right`. Wenn das Steuerelement geöffnet ist, sind zwei dieser Werte **true** und zwei **false** (beispielsweise `up` und `right`).  <br/> |Keine Parameter  <br/> |
|addEventCallback(string eventName, CalloutCallback callback  <br/> |Dient zum Registrieren einer Rückruffunktion, die aufgerufen wird, wenn das Popupsteuerelement in den Zustand wechselt, der mithilfe des Parameters  `eventName` angegeben wurde. <br/> |Der Parameter  `eventName` muss einen der folgenden Werte besitzen: `opening`,  `open`,  `closing`,  `closed`. Beim Parameter  `callback` muss es sich um eine Funktion handeln, die eine Instanz des Popupsteuerelements als ersten Parameter verwendet. <br/> |
|open()  <br/> |Dient zum Anzeigen des Steuerelements. Ist das Steuerelement bereits geöffnet, oder wird es gerade geöffnet, gibt diese Methode nur **false** zurück. <br/> |Keine Parameter  <br/> |
|close(bool useAnimation)  <br/> |Dient zum Ausblenden des Steuerelements. Ist das Steuerelement bereits geschlossen, oder wird es gerade geschlossen, gibt diese Methode nur **false** zurück. <br/> |Ein boolescher Wert, der angibt, ob das Steuerelement mit Animation geschlossen wird. Die Animation ist standardmäßig deaktiviert.  <br/> |
|toggle()  <br/> |Dient zum Wechseln des Öffnungszustands des Steuerelements.  <br/> |Keine Parameter  <br/> |
|addAction(CallOutAction calloutAction)  <br/> |Hiermit können Sie dem Array von Objekten des Typs  `CalloutAction` für das Steuerelement eine neue Aktion vom Typ `CalloutAction` hinzufügen. Mithilfe dieser Objekte werden die Aktionen definiert, die in der Fußzeile des Steuerelements angezeigt werden sollen. Im Abschnitt [So wird's gemacht: Hinzufügen von Aktionen zum Popupsteuerelement](#AddActions) erfahren Sie, wie Sie diese Objekte erstellen. Aktionen können erst nach dem Erstellen einer Instanz des Steuerelements hinzugefügt werden. Das Steuerelement kann mit maximal drei Aktionen versehen werden. Beim Versuch, noch mehr Aktionen hinzuzufügen, tritt ein Ausnahmefehler auf. <br/> |Ein  `CalloutAction`-Objekt.  <br/> |
|refreshActions()  <br/> |Lädt alle Aktionen neu, die dem Steuerelement hinzugefügt wurden. Mithilfe dieser Methode können Sie Aktionen ändern, aktivieren oder deaktivieren, während das Steuerelement geöffnet ist.  <br/> |Keine Parameter  <br/> |
 

## So wird's gemacht: Hinzufügen von Aktionen zum Popupsteuerelement
<a name="AddActions"> </a>

Aktionen werden nach Erstellung einer Instanz des Popupsteuerelements hinzugefügt. Bei einer Popupaktion kann es sich um einzelne Aktion oder um ein Menü mit Aktionen handeln. Ein Popupsteuerelement kann mit bis zu drei Aktionen versehen werden. Nach Erstellung einer Popupaktion können Sie sie dem Objekt  `CalloutControl` mit der zugehörigen Methode vom Typ `addAction` hinzufügen. Die folgende Beispielaktion öffnet ein neues Browserfenster, wenn der Benutzer auf den Text klickt:




```

//Create CalloutAction
var calloutAction = new CalloutAction({
            text: "Open window"
            onClickCallback: function() {            
                window.open(url);
            }
        });

//Add Action to an instance of the CalloutControl    
        myCalloutControl.addAction(calloutAction);
```

Sie können auch Werte für alle Elemente vom Typ  `CalloutAction` in einem Objekt vom Typ `CalloutActionOptions` festlegen und dieses Objekt an den Konstruktor `CalloutAction` übergeben:






```

//Create CalloutAction
var calloutActionOptions = new CalloutActionOptions();
calloutActionOptions.text = "Open window";
actionOptions.onClickCallback = function() {
    window.open(url);
};
var calloutAction = new CalloutAction(calloutActionOptions);

//Add Action to an instance of the CalloutControl    
        myCalloutControl.addAction(calloutAction);
```

Die folgenden Elemente dienen zum Definieren des Verhaltens einer Popupaktion:





|**Element**|**Zweck**|**Gültige Werte (Standardwert in Fettformatierung)**|
|:-----|:-----|:-----|
|text (required)  <br/> |Dient zum Anzeigen einer Beschriftung für die Aktion.  <br/> |Zeichenfolge, **NULL** <br/> |
|onClickCallback  <br/> |Dient zum Definieren der Aktion, die ausgeführt werden soll, wenn der Benutzer auf die Aktionsbeschriftung des Popups klickt.  <br/> | `function(calloutAction /*=CalloutAction*/) {...}`, **null** <br/> |
|isEnabledCallback  <br/> |Dient zum Definieren einer Rückruffunktion, die vor der Darstellung des Popups ausgeführt wird und bestimmt, ob die Aktion aktiviert ist. Gibt die Funktion **true** zurück, wird im Popup die aktivierte Aktion angezeigt. Bei **false** wird zwar der Aktionstext im Popup angezeigt, die Aktion wird jedoch deaktiviert. <br/>  `function(calloutAction /*=CalloutAction*/) {...}`, **null** <br/> |
|isVisibleCallback  <br/> |Dient zum Definieren einer Rückruffunktion, die vor der Darstellung des Popups ausgeführt wird und bestimmt, ob der Aktionstext angezeigt wird. Gibt die Funktion **true** zurück, wird im Popup der Aktionstext angezeigt. Bei **false** wird der Aktionstext nicht angezeigt. Weitere Aktionen rücken nach links, um die Position der ausgeblendeten Aktion einzunehmen. <br/> | `function(calloutAction /*=CalloutAction*/) {...}`, **NULL** <br/> |
|tooltip  <br/> |Dient zum Anzeigen von Text, wenn der Benutzer auf den Aktionstext des Popups zeigt.  <br/> |Zeichenfolge, **NULL** <br/> |
|disabledTooltip  <br/> |Dient zum Anzeigen von Text, wenn der Benutzer auf den Aktionstext des Popups zeigt und die Popupaktion deaktiviert wurde (die Funktion  `isEnabledCallback` also **false** zurückgibt). <br/> |Zeichenfolge, **NULL** <br/> |
|menuEntries  <br/> |Dient zum Definieren eines Menüs mit Aktionen anstelle einer einzelnen Aktion. Wie Sie ein Element vom Typ  `CalloutActionMenuEntry` erstellen und einem Objekt vom Typ `CalloutAction` hinzufügen, erfahren Sie im nächsten Abschnitt. <br/> |[ `CalloutActionMenuEntry`, ...], null  <br/> |
 

### So wird's gemacht: Hinzufügen von Aktionsmenüs zum Popupsteuerelement

Wenn eine Popupaktion anstelle einer einzelnen Aktion ein Menü enthält, wird dem Benutzer neben dem Aktionstext des Popups ein Abwärtspfeil angezeigt (siehe Abbildung 4).




**Abbildung 4: Menü für eine Popupaktion, wenn ein Benutzer auf den Pfeil neben der Aktionsbeschriftung klickt**








![Eine Popup-Aktion zeigt ein Menü an, wenn ein Benutzer auf den Pfeil neben der Aktionskennzeichnung klickt.](images/SP15_CalloutMenu.png)



Sie können beliebig viele Menüeinträge erstellen und der Popupaktion hinzufügen, indem Sie sie in einem Array als Wert des Elements  `menuEntries` des Objekts `CalloutAction` übergeben.






```

//Create two menu entries.
var menuEntry1 = new CalloutActionMenuEntry("Entry One", calloutActionCallbackFunction, "/_layouts/images/DOC16.GIF");
var menuEntry2 = new CalloutActionMenuEntry("Some Other Entry", calloutActionCallbackFunction, "/_layouts/images/XLS16.GIF");

//Add the menu entries to the callout action.
var calloutAction = new CalloutAction({
   text: "MENU W/ ICONS",
   menuEntries: [menuEntry1, menuEntry2]
})

//Add the callout action to the callout control.
callout.addAction(calloutAction);

```

Der Konstruktor  `CalloutActionMenuEntry` akzeptiert drei Parameter. Die ersten beiden Parameter sind erforderlich. Der dritte ist optional, kann jedoch hilfreich sein, da Sie damit ein Symbol mit dem Text anzeigen können.




- Übergeben Sie als ersten Parameter eine Zeichenfolge, um eine Beschriftung für die einzelnen Menüeinträge anzuzeigen.


- Übergeben Sie als zweiten Parameter eine Funktion, um die Aktion zu definieren, die ausgeführt werden soll, wenn der Benutzer auf den Text des Menüeintrags klickt.


- Übergeben Sie eine Zeichenfolge mit der URL für das Symbol, das links neben der Beschriftung angezeigt werden soll.



## So wird's gemacht: Erstellen und Verwalten der Instanzen des Popupsteuerelements mithilfe von CalloutManager
<a name="UseCalloutManager"> </a>

Das Singleton-Objekt  `CalloutManager` dient zum Speichern von Verweisen auf die einzelnen Objekte vom Typ `Callout` auf einer Seite. Es speichert jede Instanz des Popupsteuerelements in einem assoziativen Array, wobei der Wert `ID` der einzelnen Steuerelemente als Schlüssel fungiert. `CalloutManager` enthält Methoden, die Sie beim Erstellen und Verwalten der gespeicherten Objekte vom Typ `Callout` unterstützen.





|**Methode**|**Zweck**|**Gültige Parameterwerte**|
|:-----|:-----|:-----|
|createNew(members)  <br/> |Dient zum Erstellen eines neuen Objekts vom Typ  `Callout`. Bei dieser Aktion wird von  `CalloutManager` ein Eintrag für das Steuerelement im zugehörigen assoziativen Array erstellt. Der Wert des erforderlichen Elements `ID` fungiert dabei als Schlüssel. <br/> |Ein assoziatives Array, durch das jedem Element, das Sie verwenden möchten, Werte zugewiesen werden. Die Elemente  `ID` und `launchPoint` sind erforderlich. <br/> |
|createNewIfNecessary (members)  <br/> |Dient zum Erstellen eines Objekts vom Typ  `Callout`, falls dem als Parameter übergebenen Element  `launchPoint` nicht bereits ein Popupsteuerelement zugewiesen ist. <br/> |Ein assoziatives Array, durch das jedem Element, das Sie verwenden möchten, Werte zugewiesen werden. Die Elemente  `ID` und `launchPoint` sind erforderlich. <br/> |
|getFromLaunchPoint: function (/*@type(HTMLElement)*/launchPoint)  <br/> |Dient zum Abrufen des Objekts  `Callout`, das dem in der Funktion angegebenen Element  `launchPoint` zugeordnet ist. Von dieser Methode wird eine Ausnahme ausgelöst, wenn `launchPoint` kein Objekt vom Typ `Callout` zugewiesen ist. <br/> |Keine Parameter  <br/> |
|getFromLaunchPointIfExists: function (/*@type(HTMLElement)*/launchPoint)  <br/> |Dient zum Abrufen des Objekts  `Callout`, das dem in der Funktion angegebenen Element  `launchPoint` zugeordnet ist. Von dieser Methode wird NULL zurückgegeben, wenn `launchPoint` kein Objekt vom Typ `Callout` zugewiesen ist. <br/> |Keine Parameter  <br/> |
|getFromCalloutDescendant: function (/*@type(HTMLElement)*/descendant)  <br/> |Dient zum Abrufen des Objekts  `Callout`, das dem HTML-Element zugeordnet ist, welches im durch die Funktion angegebenen Element angegeben ist. Bei diesem Element kann es sich um ein beliebiges Nachfolgerelement des Popupelements handeln. So können Sie beispielsweise den Wert des Elements  `contentElement` übergeben, das Sie beim Erstellen des Objekts `Callout` zugewiesen haben. Von dieser Methode wird eine Ausnahme ausgelöst, wenn dem Nachfolgerelement kein Objekt vom Typ `Callout` zugeordnet ist. <br/> |Keine Parameter  <br/> |
|closeAll()  <br/> |Schließt alle geöffneten Objekte vom Typ  `Callout`. Von dieser Methode wird "true" zurückgegeben, wenn damit mindestens ein Popup geschlossen wird.  <br/> |Keine Parameter  <br/> |
|isAtLeastOneCalloutOpen()  <br/> |Dient zum Überprüfen, ob mindestens ein Popup geöffnet ist.  <br/> |Keine Parameter  <br/> |
 

## So wird's gemacht: Positionieren des Popupsteuerelements
<a name="Positioning"> </a>



|**Element**|**Zweck**|**Gültige Werte (Standardwert in Fettformatierung)**|
|:-----|:-----|:-----|
|boundingBox  <br/> |Dient zum Angeben des HTML-Elements, das als Äquivalent von  `offsetParent` des Popupsteuerelements fungiert. Standardmäßig wird hierfür der Wert `offsetParent` des Popupsteuerelements verwendet, Sie können mithilfe dieses Elements jedoch die korrekte Positionierung des Steuerelements sicherstellen. Es wird versucht, das Popupsteuerelement so zu positionieren, dass es innerhalb dieses Felds sichtbar ist. Gegebenenfalls wird die Ausrichtung (von oben nach unten oder von links nach rechts, je nach Ausrichtung der Spitze) geändert, damit es sichtbar bleibt. <br/> |beliebiges HTML-Element, **der offsetParent-Wert des HTML-Elements mit dem Popupsteuerelement** <br/> |
|positionAlgorithm  <br/> |Dient zum Überschreiben des standardmäßigen Positionierungsalgorithmus für das Popupsteuerelement. Im folgenden Abschnitt erfahren Sie, wie Sie unter Verwendung des Objekts  `calloutPositioningProxy` Positionierungsalgorithmen für das Popupsteuerelement erstellen. <br/> |**CalloutOptions.prototype.defaultPositionAlgorithm**, `function(calloutPositioningProxy) { ... }` <br/> |
 

### So wird's gemacht: Erstellen von Positionierungsalgorithmen unter Verwendung von "calloutPositioningProxy"

Das Objekt  `calloutPositioningProxy` enthält Methoden und Eigenschaften zum Überschreiben der standardmäßigen Positionierungslogik des Popupsteuerelements. Soll das Steuerelement also beispielsweise immer unterhalb und rechts des Elements `launchPoint` angezeigt werden, können Sie einen Positionierungsalgorithmus nach dem folgenden Muster erstellen:




```

function alwaysGoDownAndRight(calloutPositioningProxy)  {
    calloutPositioningProxy.moveDownAndRight();
} 

```

Die Funktion wird dann als Wert des Elements  `positionAlgorithm` des Objekts `Callout` übergeben. Dieser Schritt kann beim Erstellen des Objekts `Callout` oder später durch Festlegen des Werts ausgeführt werden.






```

callout.set({positionAlgorithm: alwaysGoDownAndRight});

```

Sie können sich jederzeit die standardmäßige Positionierungslogik ansehen, indem Sie die JavaScript-Konsole Ihres Browsers starten (beispielsweise die F12-Entwicklertools von Internet Explorer).






```

CalloutOptions.prototype.positionAlgorithm.toString()
```

Die folgenden Methoden im Objekt  `CalloutPositioningProxy` dienen zum Erstellen einer eigenen Positionierungslogik:





|**Methode**|**Beschreibung**|
|:-----|:-----|
|isCalloutTooFarTop()  <br/> |Gibt einen booleschen Wert zurück.  <br/> |
|isCalloutTooFarRight()  <br/> |Gibt einen booleschen Wert zurück.  <br/> |
|isCalloutTooFarBottom()  <br/> |Gibt einen booleschen Wert zurück.  <br/> |
|isCalloutTooFarLeft()  <br/> |Gibt einen booleschen Wert zurück.  <br/> |
|isCalloutLeftOfHardBoundingBox()  <br/> |Gibt einen booleschen Wert zurück. Bei **true** befindet sich die linke Seite des Steuerelements außerhalb des zugehörigen Containerelements. Sie ist nicht sichtbar, und der Benutzer kann keinen Bildlauf dorthin ausführen. <br/> |
|isCalloutRightOfHardBoundingBox()  <br/> |Gibt einen booleschen Wert zurück. Bei **true** befindet sich die rechte Seite des Steuerelements außerhalb des zugehörigen Containerelements. Sie ist nicht sichtbar, und der Benutzer kann keinen Bildlauf dorthin ausführen. <br/> |
|isCalloutAboveHardBoundingBox()  <br/> |Gibt einen booleschen Wert zurück. Bei **true** befindet sich der obere Teil des Steuerelements außerhalb des zugehörigen Containerelements. Er ist nicht sichtbar, und der Benutzer kann keinen Bildlauf dorthin ausführen. <br/> |
|isCalloutBelowHardBoundingBox()  <br/> |Gibt einen booleschen Wert zurück. Bei **true** befindet sich der untere Teil des Steuerelements außerhalb des zugehörigen Containerelements. Er ist nicht sichtbar, und der Benutzer kann keinen Bildlauf dorthin ausführen. <br/> |
|isOrientedUp()  <br/> |Gibt einen booleschen Wert zurück.  <br/> |
|isOrientedDown()  <br/> |Gibt einen booleschen Wert zurück.  <br/> |
|isOrientedLeft()  <br/> |Gibt einen booleschen Wert zurück.  <br/> |
|isOrientedRight()  <br/> |Gibt einen booleschen Wert zurück.  <br/> |
|moveUpAndRight()  <br/> |Gibt nichts zurück. Dient zum Ändern der Richtung des Steuerelements.  <br/> |
|moveUpAndLeft()  <br/> |Gibt nichts zurück. Dient zum Ändern der Richtung des Steuerelements.  <br/> |
|moveDownAndRight()  <br/> |Gibt nichts zurück. Dient zum Ändern der Richtung des Steuerelements.  <br/> |
|moveDownAndLeft()  <br/> |Gibt nichts zurück. Dient zum Ändern der Richtung des Steuerelements.  <br/> |
|moveTowardsOppositeQuadrant()  <br/> |Gibt nichts zurück. Dient zum Ändern der Richtung des Steuerelements.  <br/> |
|flipHorizontal()  <br/> |Gibt nichts zurück. Dient zum Ändern der Richtung des Steuerelements.  <br/> |
|flipVertical()  <br/> |Gibt nichts zurück. Dient zum Ändern der Richtung des Steuerelements.  <br/> |
|numberOfEdgesCollidingWithBoundingBox()  <br/> |Gibt eine ganze Zahl zwischen 0 und 4 zurück. Diese Zahl steht für die Anzahl von Kanten, an denen das Popup mit dem sichtbaren Begrenzungsrahmen kollidiert. Beispiel: Wird der obere Teil des Steuerelements nach dem Aufrufen der Methode  `moveUpAndRight()` durch den oberen Teil des Dokumenttexts abgeschnitten, wird von der Methode `numberOfEdgesCollidingWithBoundingBox()` eine Zahl größer 1 zurückgegeben. <br/> |
 
Der Positionierungsalgorithmus sorgt dafür, dass das Steuerelement ober- oder unterhalb des Texts positioniert wird. Die Eigenschaft  `isRTL` von `CalloutPositioningProxy` gibt Aufschluss darüber, ob es sich bei dem angezeigten Text um eine Rechts-nach-links-Sprache handelt. Durch die Überprüfung dieser Eigenschaft wird sichergestellt, dass das Steuerelement stets korrekt in Relation zum Text auf der Seite positioniert wird.






```
function examplePositionAlgorithm(calloutPositioningProxy) {
    if (!calloutPositioningProxy.isRTL) {
        calloutPositioningProxy.moveDownAndRight();
        if (calloutPositioningProxy.isCalloutTooFarBottom()) {
            calloutPositioningProxy.moveUpAndRight();
        }
    }
    else {
        calloutPositioningProxy.moveDownAndLeft();
        if (calloutPositioningProxy.isCalloutTooFarBottom()) {
            calloutPositioningProxy.moveUpAndLeft();
        }
    }
}
callout.set({positionAlgorithm: examplePositionAlgorithm});

```

Der folgende Positionierungsalgorithmus ändert die Standardrichtung des Steuerelements von  `upAndRight` in `downAndRight`, greift im Falle von Kollisionen aber auf den Standardalgorithmus zurück:






```

function tryDownAndRightThenGoDefault(calloutPositioningProxy) {
    if (!calloutPositioningProxy.isRTL)
        calloutPositioningProxy.moveDownAndRight();
    else
        calloutPositioningProxy.moveDownAndLeft();

    if (calloutPositioningProxy.numberOfEdgesCollidingWithBoundingBox() > 0)
        return CalloutOptions.prototype.positionAlgorithm.apply(this, arguments);
};
callout.set({positionAlgorithm: tryDownAndRightThenGoDefault});

```


## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [SharePoint 2013: Verwenden von Listenansichten, Popups und Dialogfeldern in von SharePoint gehosteten Add-Ins](http://code.msdn.microsoft.com/officeapps/SharePoint-2013-Use-list-cb3e4e14)


-  [Erste Schritte beim Erstellen von von SharePoint gehosteten SharePoint-Add-Ins](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)


-  [Erstellen eines einfachen von SharePoint gehosteten Add-Ins mithilfe von Napa Office 365-Entwicklungstools](create-a-basic-sharepoint-hosted-add-in-by-using-napa-office-365-development-too.md)


-  [UX-Design für SharePoint-Add-Ins](ux-design-for-sharepoint-add-ins.md)






