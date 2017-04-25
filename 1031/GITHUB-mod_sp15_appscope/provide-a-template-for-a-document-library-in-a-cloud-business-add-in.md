---
title: Bereitstellen einer Vorlage für eine Dokumentbibliothek in einem Cloud-Geschäfts-Add-In
ms.prod: SHAREPOINT
ms.assetid: 78df5ac7-a832-4fd0-89dc-9e440eefd7c8
---


# Bereitstellen einer Vorlage für eine Dokumentbibliothek in einem Cloud-Geschäfts-Add-In
Zusätzlich zu den Office-Vorlagen, die beim Hinzufügen eines Dokuments zu einer SharePoint-Dokumentbibliothek verfügbar sind, können Sie auch Ihre eigenen Vorlagen verwenden. Sie haben möglicherweise Ihre eigene Verkaufsangebotsvorlage, die Sie verwenden möchten, wenn neue Aufträge hinzugefügt werden.
## 

Falls noch nicht geschehen, verknüpfen Sie eine Dokumentbibliothek mit Ihrem Cloud-Geschäfts-Add-In. Weitere Informationen dazu finden Sie unter  [Zuordnen einer Dokumentbibliothek zu einer Entität](associate-a-document-library-with-an-entity.md).




### So fügen Sie eine Vorlage hinzu


1. Wechseln Sie zu Ihrer SharePoint-Entwicklerwebsite und klicken Sie anschließend auf der Seite **Entwickler** auf **Websiteinhalte**.


2. Klicken Sie auf der Seite **Websiteinhalte** auf **Einstellungen**, wie in Abbildung 1 dargestellt.

   **Abbildung 1. Der Link „Einstellungen"**



!\[Link "Websiteeinstellungen"](images/CBA_IM_8b.PNG)





3. Klicken Sie auf der Seite **Websiteeinstellungen** in der Liste **Web-Designer-Kataloge** auf **Websiteinhaltstypen**, wie in Abbildung 2 dargestellt.

   **Abbildung 2. Der Link „Websiteinhaltstypen"**



!\[Link "Websiteinhaltstypen"](images/CBA_IM_26.PNG)





4. Klicken Sie auf der Seite **Websiteinhaltstypen** auf **Erstellen**, wie in Abbildung 3 dargestellt.

   **Abbildung 3. Der Link „Erstellen"**



!\[Link "Erstellen"](images/CBA_IM_27.PNG)





5. Geben Sie auf der Seite **Neuer Websiteinhaltstyp** den Namen und die Beschreibung für Ihre Vorlage ein. Wählen Sie bei **Übergeordneter Inhaltstyp** **Dokumentinhaltstyp** und **Dokument** aus, wie in Abbildung 4 dargestellt.

   **Abbildung 4. Auswahl des übergeordneten Inhaltstyps**



!\[Übergeordnete Inhaltstypauswahlen](images/CBA_IM_28.PNG)





6. Klicken Sie im Abschnitt **Gruppe** in der Liste **Vorhandene Gruppe** auf **Dokumentinhaltstypen** wie in Abbildung 5 dargestellt, und anschließend auf **OK**.

   **Abbildung 5. Gruppeneinstellung**



!\[Gruppeneinstellung](images/CBA_IM_28a.PNG)





7. Klicken Sie auf der Seite **Websiteinhaltstyp** auf **Erweiterte Einstellungen**.


8. Geben Sie auf der Seite **Erweiterte Einstellungen** entweder die URL einer vorhandenen Dokumentvorlage ein oder laden Sie eine neue Dokumentvorlage hoch wie in Abbildung 6 dargestellt, und klicken Sie anschließend auf **OK**.

   **Abbildung 6. Angeben der Dokumentvorlage**



!\[Dokumentvorlage angeben](images/CBA_IM_29.PNG)





9. Wechseln Sie zur Seite **Websiteinhalte**, klicken Sie auf Ihre Dokumentbibliothek, und wechseln Sie dann auf die Seite **Einstellungen**.


10. Klicken Sie auf der Seite **Einstellungen** auf **Aus vorhandenen Websiteinhaltstypen hinzufügen**.


11. Fügen Sie Ihre Vorlage auf der Seite **Inhaltstypen hinzufügen** wie in Abbildung 7 dargestellt hinzu, und klicken Sie anschließend auf **OK**.

   **Abbildung 7. Hinzufügen der Vorlage**



!\[Hinzufügen der Vorlage](images/CBA_IM_29a.PNG)





12. Starten Sie Ihr Add-In, und fügen Sie ein Dokument hinzu. Ihre Vorlage sollte im Dialogfeld **Neue Datei erstellen** angezeigt werden, wie in Abbildung 8 dargestellt.

   **Abbildung 8. Das Dialogfeld „Neue Datei erstellen" mit der neuen Vorlage**



!\[Das Dialogfeld "Neue Datei erstellen" mit der neuen Vorlage](images/CBA_IM_30.PNG)






## Zusätzliche Ressourcen
<a name="bk_addresources"> </a>


-  [Entwickeln von Cloud-Geschäfts-Add-Ins](develop-cloud-business-add-ins.md)


-  [Zuordnen einer Dokumentbibliothek zu einer Entität](associate-a-document-library-with-an-entity.md)



