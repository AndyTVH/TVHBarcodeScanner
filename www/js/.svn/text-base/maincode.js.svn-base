// Overzicht globale variabelen
// code = barcode ingevoerd in nieuwe scan pagina
// aantal = aantal ingevoerd in nieuwe scan pagina
// lijstnaam = nieuwe lijstnaam ingevoerd in nieuwe lijst pagina
// lijstenArray = array met de namen van de bestaande lijsten - wordt iedere keer
// aangepast
// barcodeArray = array met de barcodes van de producten in de actieve lijst
// lijstid = nummer van de actieve lijst
// titel = naam van de actieve lijst
// detailid = id van het actieve product
// barcodeVoorUpdate = barcode ingevuld op de detailpagina van het actieve
// product
// keuze = welke optie om in te voeren is gekozen?

document.addEventListener("deviceready", onDeviceReady, false);
var db = window.openDatabase("Dummy_DB", "1.0", "Testdatabase", 200000);
// knoppen en tekstvakken binden
$(function() {
	$("#btn_NewList").bind("click", function(){
		$.mobile.changePage("#nieuweLijst");
	});
	$("#btn_ScanIntoList").bind("click", function(){
		if(keuze == 1){
			$("#btn_ScanButton").trigger("click");
			$.mobile.changePage("#scan");
		} else if(keuze == 3){
			$("#txt_TVHCodeInsert, #txt_TVHCodeAmount").val("");
			$.mobile.changePage("#tvhcodeinvoer");
		} else{
			$("#txt_ScannedCode").val("");
			$("#txt_ScannedAmount").val("1");
			$.mobile.changePage("#scan");
			$("#txt_ScannedCode").focus();
		}		
	});
	$("#btn_RemoveList").bind("click", function(){
		dialogueRemoveList();
	});
	$("#btn_SendList").bind("click", function(){
		alert("You have pressed the send button.");
	});
	$("#btn_ExportDetail").bind("click", function(){
		db.transaction(exportCall, errorCB);
		$.mobile.changePage("#exporteer");
	});
	$("#btn_UpdateDetail").bind("click", function() {
		$.mobile.changePage("#lijstdetailaanpassen");
		$("#txt_BarcodeUpdated").val(barcodeVoorUpdate);
		$("#txt_AmountUpdated").val("");
	});
	$("#btn_DeleteDetail").bind("click", function() {
		$(".ui-footer, .ui-header").hide();
		$("<div>").simpledialog2({
			mode : "button",
			headerText : header_dialog_DeleteDetail,
			headerClose : false,
			buttonPrompt : msg_dialog_DeleteDetail,
			buttons : {
				"OK" : {
					click : function() {
						$(".ui-footer, .ui-header").show();
						db.transaction(deleteFromDetail, errorCB, successDetailsCB);
						$.mobile.loading('show', {
							theme:'a'
						});
						setTimeout(function(){
							$.mobile.changePage("#detailsVanLijst");
							$.mobile.loading('hide');
						}, 500);
					},
					theme:"e"
				},
				"Cancel" : {
					click : function() {
						$(".ui-footer, .ui-header").show();
						$.mobile.changePage("#detailInfo");
					},
					icon:"delete",
					theme:"c"
				}
			}
		});
	});
	$("#btn_CreateNewList").bind("click", addNewList);
	$("#btn_NewDetail").bind("click", addNewListDetail);
	$("#btn_UpdateDetailConfirm").bind("click", detailUpdate);
	$("#btn_NewDetailEntry").bind("click", insertNewTVHCode);
	$("#btn_NewItemFromSAYG").bind("click", function() {
		var make = $("#slct_Make").val();
		var partNumber = $("#txt_PartnumberInsert").val();
		if (make == "") {
			$("#popup_Make").popup("open", {positionTo : $("#slct_Make")});
		} else if (partNumber == "") {
			$("#popup_Partnumber").popup("open", {positionTo : $("#txt_PartnumberInsert")});
		} else {
			db.transaction(insertItemMapping, errorCB);
			$.mobile.loading('show', {
				theme:'a'
			});
			setTimeout(function(){
				$.mobile.changePage('#items');
				$.mobile.loading('hide');
			}, 500);
		}
	});
	$("#btn_UpdateItem").bind("click", function() {
		var make = $("#slct_MakeUpdate").val();
		var partNumber = $("#txt_PartnumberUpdate").val();
		if (make == "") {
			$("#popup_MakeUpdate").popup("open", {positionTo : $("#slct_MakeUpdate")});
		} else if (partNumber == "") {
			$("#popup_PartnumberUpdate").popup("open", {positionTo : $("#txt_PartnumberUpdate")});
		} else {
			db.transaction(updateItemMapping, errorCB);
			$.mobile.loading('show', {
				theme:'a'
			});
			setTimeout(function(){
				$.mobile.changePage("#itemdetail");
				$.mobile.loading('hide');
			},500);
		}
	});
	$("#btn_ScanNewItem").bind("click", function(){
		if(keuze==1){
			$("#btn_ScanButton").trigger("click");
			$.mobile.changePage("#nieuwItem");
			$("#slct_Make").focus();
		} else {
			$("#txt_BarcodeInsert").val("");
			$.mobile.changePage("#nieuwItem");
			$("#txt_BarcodeInsert").focus();
		}
	});
	$("#btn_SetItemmapping").bind("click", function(){
		if(keuze == 1){
			db.transaction(selectAllItems, errorCB);
			$.mobile.changePage("#barcodemappinginvoer");
		} else {
			db.transaction(selectAllItems, errorCB);
			$.mobile.changePage("#itemmappingmanueel");
		}
	});
	$("#btn_UpdateItemInfo").bind("click", function(){
		$.mobile.changePage("#itemUpdate");
	});
	$("#btn_DeleteItem").bind("click", function(){
		db.transaction(deleteFromItemMapping, errorCB);
	});
	$("#btn_NextBarcode").bind("click", function(){
		var mappingBarcode = $("#txt_BarcodeForMapping").val();
		if(mappingBarcode!=""){
			if(itemMapIndex < itemMap.length-1){
				itemArrayUpdated.push([itemMap[itemMapIndex][0], itemMap[itemMapIndex][1], mappingBarcode, itemMap[itemMapIndex][2]]);
				itemMapIndex++;
				$("#codedisplay").text("");
				$("#txt_BarcodeForMapping").val("");	
				$("#codedisplay").append(itemMap[itemMapIndex][2] + "-" + itemMap[itemMapIndex][1]);
			}
			else {
				itemArrayUpdated.push([itemMap[itemMapIndex][0], itemMap[itemMapIndex][1], mappingBarcode, itemMap[itemMapIndex][2]]);
				for(var i=0; i<itemArrayUpdated.length; i++){
					$("#lstvw_Mapping").append('<li text-overflow:clip>' + itemArrayUpdated[i][3] + '-' + itemArrayUpdated[i][1] + '<br/>' + itemArrayUpdated[i][2]);
				}
				$.mobile.changePage("#barcodemapping");
				$("#codedisplay").text("");
				$("#lstvw_Mapping").listview("refresh");
			}
		} else {
			$("#popup_MappingEmpty").popup("open", {positionTo:$("#txt_BarcodeForMapping")});
		}		
	});
	$("#btn_NextBarcodeManually").bind("click", function(){
		var mappingBarcode = $("#txt_BarcodeMappingManual").val();
		if(mappingBarcode!=""){
			if(itemMapIndex < itemMap.length-1){
				itemArrayUpdated.push([itemMap[itemMapIndex][0], itemMap[itemMapIndex][1], mappingBarcode, itemMap[itemMapIndex][2]]);
				itemMapIndex++;
				$("#codedisplaymanueel").text("");
				$("#txt_BarcodeMappingManual").val("");
				$("#codedisplaymanueel").append(itemMap[itemMapIndex][2] + "-" + itemMap[itemMapIndex][1]);
			}
			else{
				itemArrayUpdated.push([itemMap[itemMapIndex][0], itemMap[itemMapIndex][1], mappingBarcode, itemMap[itemMapIndex][2]]);
				for(var i=0; i<itemArrayUpdated.length; i++){
					$("#lstvw_Mapping").append('<li text-overflow:clip>' + itemArrayUpdated[i][3] + '-' + itemArrayUpdated[i][1] + '<br/>' + itemArrayUpdated[i][2]);
				}
				$.mobile.changePage("#barcodemapping");
				$("#codedisplaymanueel").text("");
				$("#lstvw_Mapping").listview("refresh");
			}
		} else {
			$("#popup_MappingManuallyEmpty").popup("open", {positionTo:$("#txt_BarcodeMappingManual")});
		}
	});
	$("#btn_ConfirmMapping").bind("click", function(){
		db.transaction(adjustMapping, errorCB);
		$.mobile.changePage("#main");
	});
	$("#btn_SetScanOption").bind("click", function(){
		db.transaction(setScanOption, errorCB);
		$.mobile.changePage("#settings");
		$("#popup_ScanUpdate").popup("open", {positionTo:$("#btn_SortMapping")});
	});
	$("#btn_SetSortOption").bind("click", function(){
		db.transaction(setSort, errorCB);
		$.mobile.changePage("#settings");
		$("#popup_SortUpdate").popup("open",{positionTo:$("#btn_SortMapping")});
	});
	$("#btn_SetLanguageOption").bind("click", function(){
		db.transaction(setLanguage, errorCB);
	});
	$("#btn_UserData").bind("click", function(){
		db.transaction(checkDataExists, errorCB);
	});
	$("#btn_setscanmode").bind("click", function(){
		$.mobile.changePage("#scansettings");
	});
	$("#btn_SortMapping").bind("click", function(){
		$.mobile.changePage("#sorteringinstellen");
	});
	$("#btn_SetLanguage").bind("click", function(){
		$.mobile.changePage("#taalinstelling");
	});
	$("#btn_GetMakeList").bind("click", function(){
		$.mobile.loading('show',{
			text: loadingText,
			textVisible:true,
			theme:"a"
		});
		var fileTransfer = new FileTransfer();
		fileTransfer.download("http://172.16.108.255:8080/WicketApplication", "file://sdcard/makelist.json",
				function(entry){
			$.mobile.loading('hide');
			$(".ui-footer, .ui-header").hide();
			$('<div>').simpledialog2({
				mode:'button',
				buttonPrompt : "<strong>" + header_dialog_downloadOK + "</strong>",
				headerClose: false,
				buttons:{
					"OK":{
						click: function(){
							$(".ui-footer, .ui-header").show();
						},
						theme:"e"
					}
				}
			});
			var request = new XMLHttpRequest();
			request.open("GET", "file://sdcard/makelist.json");
			request.onreadystatechange = function(){
				if(request.readyState == 4){
					var info = JSON.parse(request.responseText);
					for(var i=0; i<info.length;i++){
						makelist = new Array();
						makelist.push([info.MakeCode],[info.MakeDescription]);
					}
				}
			};
			request.send();
			db.transaction(createMakeTable, errorCB);
		},
		function(error){
			$.mobile.loading('hide');
			$(".ui-footer, .ui-header").show();
			$('<div>').simpledialog2({
				mode:'button',
				buttonPrompt: "<strong>" + header_dialog_downloadfail + "</strong>",
				headerClose:false,
				buttons:{
					"OK":{
						click: function(){
							$(".ui-footer, .ui-header").show();
						},
						theme:"e"
					}
				}
			});
		});
	});
	$("#btn_SetUserData").bind("click", function(){
		var gebruikersnaam = $("#txt_UserName").val();
		var bedrijfsnaam = $("#txt_Company").val();
		var klantnummer = $("#txt_ClientCode").val();
		var paswoord = $("#txt_Password").val();
		if(gebruikersnaam == ""){
			$("#popup_Username").popup("open", {positionTo:$("#txt_UserName")});
		} else if(bedrijfsnaam == ""){
			$("#popup_Company").popup("open", {positionTo: $("#txt_Company")});
		} else if (paswoord == ""){
			$("#popup_Password").popup("open", {positionTo: $("#txt_Password")});
		} else if(klantnummer == ""){
			$("#popup_ClientCode").popup("open", {positionTo: $("#txt_ClientCode")});
		} else {
			db.transaction(insertUserData, errorCB);
			$.mobile.changePage("#settings");
		}
	});	
	$("#btn_ConfirmItemSAYG").bind("click", function() {
		var make = $("#slct_MakeInsertSAYG").val();
		var partNumber = $("#txt_PartnumberInsertSAYG").val();
		if (make == "") {
			$("#popup_MakeSAYG").popup("open", {positionTo : $("#slct_MakeInsertSAYG")});
		} else if (partNumber == "") {
			$("#popup_PartnumberSAYG").popup("open", {positionTo : $("#txt_PartnumberInsertSAYG")});
		} else {
			db.transaction(insertItemMappingSAYG, errorCB);
			$.mobile.changePage("#scan");
		}
	});
	$("#btn_ConfirmExport").bind("click", function(){
		db.transaction(exportToOtherList, errorCB);
		$.mobile.loading("show", {
			theme:'a'
		});
		setTimeout(function(){
			$.mobile.changePage("#mylists");
			$.mobile.loading('hide');
		}, 500);
	});
	//bepaalde tekstvakjes leegmaken als er op gefocust wordt
	$("#slct_MakeUpdate").bind("focus", function() {
		$("#slct_MakeUpdate").val("");
	});
	$("#txt_PartnumberUpdate").bind("focus", function() {
		$("#txt_PartnumberUpdate").val("");
	});
	$("#txt_CustomerRefUpdate").bind("focus", function() {
		$("#txt_CustomerRefUpdate").val("");
	});
	$("#DescriptionUpdate").bind("focus", function() {
		$("#DescriptionUpdate").val("");
	});
	$("#txt_ExportAmount").bind("focus", function(){
		$("#txt_ExportAmount").val("");
	});
	$("#txt_ScannedAmount").bind("focus", function(){
		$("#txt_ScannedAmount").val("");
	});
	//bepaalde queries laten uitvoeren op pageload
	$("#items").bind("pageshow", function(){
		db.transaction(queryItemsDB,errorCB);
	});
	$("#itemdetail").bind("pageshow", function(){
		db.transaction(queryItemDetail, errorCB);
	});
	$("#gegevensweergave").bind("pageshow", function(){
		db.transaction(queryUserData, errorCB);
	});
	$("#mylists").bind("pageshow", function(){
		db.transaction(successListCB, errorCB);
	});
});
//een aantal functies om focus van een net ingevuld vak naar het
//volgende tekstvak te brengen met het toetsenbord
function nieuwelijstsubmit(event){
	event.preventDefault();
	$("#txt_ListName").blur();
	$("#btn_CreateNewList").focus();
}
function naarAantalKnop(event) {
	event.preventDefault();
	$("#txt_ScannedCode").blur();
	$("#txt_ScannedAmount").focus();
}
function nieuwListDetailSubmit(event){
	event.preventDefault();
	$("#txt_ScannedAmount").blur();
	$("#btn_NewDetail").focus();
}
function updateListDetailBarcodeSubmit(event){
	event.preventDefault();
	$("#txt_BarcodeUpdated").blur();
	$("#txt_AmountUpdated").focus();
}
function updateListDetailAantal(event){
	event.preventDefault();
	$("#txt_AmountUpdated").blur();
	$("#btn_UpdateDetailConfirm").focus();
}
function makeIngevuld(event){
	event.preventDefault();
	$("#slct_MakeInsert").blur();
	$("#txt_TVHCodeInsert").focus();
}
function partnumberIngevuld(event){
	event.preventDefault();
	$("#txt_TVHCodeInsert").blur();
	$("#txt_TVHCodeAmount").focus();
}
function partnumberIngevuldSubmit(event){
	event.preventDefault();
	$("#txt_TVHCodeAmount").blur();
	$("#btn_NewDetailEntry").focus();
}
function makeSubmit(event) {
	event.preventDefault();
	$("#slct_Make").blur();
	$("#txt_PartnumberInsert").focus();
}
function partnumberSubmit(event) {
	event.preventDefault();
	$("#txt_PartnumberInsert").blur();
	$("#txt_CustomerRefInsert").focus();
}
function customerrefSubmit(event) {
	event.preventDefault();
	$("#txt_CustomerRefInsert").blur();
	$("#btn_NewItemFromSAYG").focus();
}
function makeupdate(event) {
	event.preventDefault();
	$("#slct_MakeUpdate").blur();
}
function partnumberupdate(event) {
	event.preventDefault();
	$("#txt_PartnumberUpdate").blur();
}
function customerrefupdate(event) {
	event.preventDefault();
	$("#txt_CustomerRefUpdate").blur();
}
function gebruikersnaamform(event){
	event.preventDefault();
	$("#txt_UserName").blur();
	$("#txt_Company").focus();
}
function bedrijfsform(event){
	event.preventDefault();
	$("#txt_Company").blur();
	$("#txt_ClientCode").focus();
}
function klantnummerform(event){
	event.preventDefault();
	$("#txt_ClientCode").blur();
	$("#txt_Password").blur();
}
function paswoordform(event){
	event.preventDefault();
	$("#txt_Password").blur();
	$("#btn_SetUserData").focus();
}
function makeSAYGSubmit(event){
	event.preventDefault();
	$("#slct_MakeInsertSAYG").blur();
	$("#txt_PartnumberInsertSAYG").focus();
}
function partnumberSAYGSubmit(event){
	event.preventDefault();
	$("#txt_PartnumberInsertSAYG").blur();
	$("#txt_CustomerRefInsertSAYG").focus();
}
function customerrefSAYGSubmit(event){
	event.preventDefault();
	$("#txt_CustomerRefInsertSAYG").blur();
	$("#btn_ConfirmItemSAYG").focus();
}
function aantalexportSubmit(event){
	event.preventDefault();
	$("#txt_ExportAmount").blur();
	$("#slct_Lists").focus();
}
function selectboxSubmit(event){
	event.preventDefault();
	$("#slct_Lists").blur();
	$("#btn_ConfirmExport").focus();
}
// function will be called when device ready - tabellen en basisknoppen klaarmaken
function onDeviceReady() {
	$.mobile.defaultPageTransition="none";
	db.transaction(populateLists, errorCB);
	db.transaction(populateSettings, errorCB);
	db.transaction(populateItemMapping, errorCB);
	db.transaction(populateDetails, errorCB);
	db.transaction(createData, errorCB);
	db.transaction(createMake, errorCB);
	db.transaction(querySetting, errorCB);
	db.transaction(queryItemsGemapt, errorCB);
	db.transaction(querySortSetting, errorCB);
	db.transaction(setSelectors, errorCB);
	//swipen werkt, maar misschien iets beter afstellen
	//$(document).on("swipe", "#listitem", function(){
	//	alert("test swipe");
	//});
	//db.transaction(queryLanguageSetting, errorCB);
	var taal;
	navigator.globalization.getLocaleName(
			function(locale){
				taal = locale.value.substring(0,2);
			},
			function(){
				taal="en";
			});
	loadAndDisplayLanguage(taal);
	document.addEventListener("menubutton", onMenuKeyDown, false);
	document.addEventListener("touchmove", function(event){event.preventDefault();}, false);
	document.addEventListener("backbutton", function() {
		if ($.mobile.activePage.attr("id") == "main") {
			$(".ui-footer").hide();
			$("<div>").simpledialog2({
				mode:'button',
				headerText:quitHeader,
				headerClose:false,
				buttonPrompt: quitText,
				buttons:{
					"OK":{
						click: function(){
							navigator.app.exitApp();
						},
						theme:"e"
					},
					"Cancel":{
						click:function(){
							$.mobile.changePage("#main");
							$(".ui-footer").show();
						}, 
						icon:"delete",
						theme:"c"
					}
				}
			});
		} else if($.mobile.activePage.attr("id")=="barcodemappinginvoer"||$.mobile.activePage.attr("id")=="barcodemapping"||$.mobile.activePage.attr("id")=="itemmappingmanueel"){		
		} else {
			navigator.app.backHistory();
		}
	}, true);
	setTimeout(function(){
		navigator.splashscreen.hide();
	}, 800);
}
function populateLists(tx){      	
	//tx.executeSql('DROP TABLE IF EXISTS ListHeader');       	
	tx.executeSql("CREATE TABLE IF NOT EXISTS ListHeader (ListNumber INTEGER PRIMARY KEY AUTOINCREMENT, ListName TEXT NOT NULL, ListDate TEXT NOT NULL DEFAULT CURRENT_DATE, LastUpdated TEXT NOT NULL DEFAULT CURRENT_DATE)");   
	/*tx.executeSql('INSERT INTO ListHeader(ListName, ListDate, LastUpdated) VALUES("Onderdelen 1", "2012-08-01", "2012-09-25")');
	tx.executeSql('INSERT INTO ListHeader(ListName, ListDate, LastUpdated) VALUES("Zending Japan", "2012-09-27", "2012-12-08")');
	tx.executeSql('INSERT INTO ListHeader(ListName, ListDate, LastUpdated) VALUES("Vorken", "2012-10-11", "2013-01-06")');
	tx.executeSql('INSERT INTO ListHeader(ListName, ListDate, LastUpdated) VALUES("Onderdelen 2", "2012-10-24", "2013-01-05")');*/
}
// de commentaar regels zijn enkel om snel een prepopulated database te krijgen
function populateDetails(tx) {
	//tx.executeSql('DROP TABLE IF EXISTS ListDetail');
	tx.executeSql("CREATE TABLE IF NOT EXISTS ListDetail (ListDetail_id INTEGER PRIMARY KEY AUTOINCREMENT, ListHeader_ListNumber INTEGER NOT NULL , barcode INTEGER NOT NULL, NrOfItems INTEGER NOT NULL, FOREIGN KEY (ListHeader_ListNumber) REFERENCES ListHeader(ListNumber), FOREIGN KEY (barcode) REFERENCES ItemMapping(CustomerBarcode))");        	
	/*tx.executeSql('INSERT INTO ListDetail(ListHeader_ListNumber, barcode, NrOfItems) VALUES (1, 4011462791708, 5)');
	tx.executeSql('INSERT INTO ListDetail(ListHeader_ListNumber, barcode, NrOfItems) VALUES (1, 5902013930853, 10)');
	tx.executeSql('INSERT INTO ListDetail(ListHeader_ListNumber, barcode, NrOfItems) VALUES (2, 718103128063, 2)');
	tx.executeSql('INSERT INTO ListDetail(ListHeader_ListNumber, barcode, NrOfItems) VALUES (3, 5902013930853, 1)');
	tx.executeSql('INSERT INTO ListDetail(ListHeader_ListNumber, barcode, NrOfItems) VALUES (4, 4902505122439, 16)');
	tx.executeSql('INSERT INTO ListDetail(ListHeader_ListNumber, barcode, NrOfItems) VALUES (4, 5410631063216, 9)');*/
}	
// TODO populateItemMapping is oorspronkelijk leeg, de waarden ervan moeten ontvangen worden via JSON en zo moet de tabel gevuld worden 
// TODO Kijken hoe dit oorspronkelijk gesorteerd moet worden als dit gemapt moet worden
function populateItemMapping(tx) {
	tx.executeSql('DROP TABLE IF EXISTS ItemMapping');
	tx.executeSql("CREATE TABLE IF NOT EXISTS ItemMapping (Make TEXT NOT NULL, Partnumber TEXT NOT NULL, CustomerRef TEXT, Description TEXT, CustomerBarcode INTEGER, New INTEGER DEFAULT 0, PRIMARY KEY(Make, Partnumber))");
	tx.executeSql('INSERT INTO ItemMapping(Make, Partnumber, CustomerRef, Description) VALUES ("102", "2751879", "Startbatterij droog 12V", null)');
	tx.executeSql('INSERT INTO ItemMapping(Make, Partnumber, CustomerRef, Description) VALUES ("116", "12721949", "Oliekeerring", "X277Z")');
	tx.executeSql('INSERT INTO ItemMapping(Make, Partnumber, CustomerRef, Description) VALUES ("105", "313997", "Bougiekabelset (5)", null)');
	tx.executeSql('INSERT INTO ItemMapping(Make, Partnumber, CustomerRef, Description) VALUES ("104", "2748539", null, "995F4")');
	tx.executeSql('INSERT INTO ItemMapping(Make, Partnumber, CustomerRef, Description) VALUES ("107", "11191877", "Rubberkoppeling 15 gaten", null)');
	/*tx.executeSql('INSERT INTO ItemMapping(Make, Partnumber, CustomerRef, Description) VALUES ("115", "1624", null, "5Xdd12")');
	tx.executeSql('INSERT INTO ItemMapping(Make, Partnumber, CustomerRef, Description) VALUES ("108", "4634753", "Rupsband 300 mm", null)');
	tx.executeSql('INSERT INTO ItemMapping(Make, Partnumber, CustomerRef, Description) VALUES ("114", "12436832", "Vork", "99H55J")');
	tx.executeSql('INSERT INTO ItemMapping(Make, Partnumber, CustomerRef, Description) VALUES ("107", "14579", "Ovale spiegel", null)');
	tx.executeSql('INSERT INTO ItemMapping(Make, Partnumber, CustomerRef, Description) VALUES ("102", "7899859", "Stainless steel steekwagen", null)');
	tx.executeSql('INSERT INTO ItemMapping(Make, Partnumber, CustomerRef, Description) VALUES ("111", "10893341", null, "X007")');*/
}
function populateSettings(tx){
	tx.executeSql('DROP TABLE IF EXISTS Settings');
	tx.executeSql('CREATE TABLE IF NOT EXISTS Settings (id INTEGER PRIMARY KEY AUTOINCREMENT, Key TEXT, Value TEXT)');
	tx.executeSql("INSERT INTO Settings (Key, Value) VALUES ('ScanMode', '1')");
	tx.executeSql("INSERT INTO Settings (Key, Value) VALUES ('ItemMapping', '0')");
	tx.executeSql("INSERT INTO Settings (Key, Value) VALUES ('UserData', '1')");
	tx.executeSql("INSERT INTO Settings (Key, Value) VALUES ('SortMode', '2')");
	//tx.executeSql("INSERT INTO Settings (Key, Value) VALUES ('Taalinstelling', null )");*/
}
function createData(tx){
	//tx.executeSql("DROP TABLE IF EXISTS Gegevens");
	tx.executeSql("CREATE TABLE IF NOT EXISTS Gegevens (id INTEGER PRIMARY KEY AUTOINCREMENT, Naam TEXT, Bedrijf TEXT, Klantnummer TEXT, Paswoord TEXT)");
}
function createMake(tx){
	//tx.executeSql("DROP TABLE IF EXISTS CustomerMake");
	tx.executeSql('CREATE TABLE IF NOT EXISTS CustomerMake (MakeCode TEXT PRIMARY KEY, MakeDescription text)');
	/*tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('101', 'Baoli')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('102', 'Caterpillar')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('103', 'Daewoo')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('104', 'Doosan')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('105', 'Hyster')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('106', 'Komatsu')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('107', 'Linde')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('108', 'Mitsubishi')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('109', 'Nichiyu')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('110', 'Nissan')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('111', 'Om-Pimespo')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('112', 'Still')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('113', 'Sumitomo')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('114', 'TCM')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('115', 'Toyota')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('116', 'Yale')");
	tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES ('117', 'Overig')");*/
}
//fill all selectmenus in the application
function setSelectors(tx){
	tx.executeSql("SELECT * FROM CustomerMake", [], fillSelectors, errorCB);
	function fillSelectors(tx, result){
		$("#slct_MakeInsertSAYG, #slct_Make, #slct_MakeUpdate, #slct_MakeInsert").empty();
		$("#slct_MakeInsertSAYG, #slct_Make, #slct_MakeUpdate, #slct_MakeInsert").append('<option>Choose one...</option>');
		for(var i=0;i<result.rows.length;i++){
			var row = result.rows.item(i);
			$("#slct_MakeInsertSAYG, #slct_Make, #slct_MakeUpdate, #slct_MakeInsert").append(
					'<option value="' + row['MakeCode'] + '">'
					+ row['MakeDescription'] + '</option>');
		}
		$("#slct_MakeInsertSAYG, #slct_Make, #slct_MakeUpdate, #slct_MakeInsert").selectmenu('refresh');
	}
}
//Backbutton function on the main page
function onConfirmQuit(button){
	if(button == "1"){
		navigator.app.exitApp();
	}
}
function onMenuKeyDown(){
}
function successListCB() {
	db.transaction(queryListDB, errorCB);
}
function errorCB(err) {}
//Fill the count bubble in the list listview:
// - part 1 are the empty lists, without this part, the empty lists wouldn't be visible
// - part 2 are the not empty lists, with the count (count function doesn't find the empty lists)
function queryListDB(tx) {
	tx.executeSql("SELECT ListNumber, ListName, ListDate, LastUpdated, 0 as aantal " +
			"FROM ListHeader " +
			"WHERE ListNumber NOT IN (SELECT ListHeader_ListNumber FROM ListDetail) " +
			"UNION " +
			"SELECT ListNumber, ListName, ListDate, LastUpdated, COUNT(*) as aantal " +
			"FROM ListHeader INNER JOIN ListDetail ON ListNumber = ListHeader_ListNumber " +
			"GROUP BY ListNumber",
			[], querySuccessList, errorCB);
}
//fill listview van listheaders 
function querySuccessList(tx, result) {
	$("#lstvw_Lists").empty();
	lijstenArray = new Array();
	lijstenArrayMap = {};
	var taal;
	navigator.globalization.getLocaleName(
			function(locale){
				taal = locale.value.substring(0,2);
			},
			function(){
				taal="en";
			});
	for (var i=0;i<result.rows.length;i++){
		var row = result.rows.item(i);
		var dateCreate = row['ListDate'];
		var dateUpdated = row['LastUpdated'];
		var splitDateCreate = dateCreate.split("-");
		var splitDateUpdated = dateUpdated.split("-");
		var yearCreate = splitDateCreate[0];
		var monthCreate = splitDateCreate[1];
		var dayCreate = splitDateCreate[2];
		var yearUpdate = splitDateUpdated[0];
		var monthUpdate = splitDateUpdated[1];
		var dayUpdate = splitDateUpdated[2];
		switch(monthCreate){
			case "01":
				monthCreateWords = "Jan.";
				break;
			case "02":
				monthCreateWords = "Feb.";
				break;
			case "03":
				monthCreateWords = "Mar.";
				break;
			case "04":
				monthCreateWords = "Apr.";
				break;
			case "05":
				monthCreateWords = "May";
				break;
			case "06":
				monthCreateWords = "Jun.";
				break;
			case "07":
				monthCreateWords = "Jul.";
				break;
			case "08":
				monthCreateWords = "Aug.";
				break;
			case "09":
				monthCreateWords = "Sep.";
				break;
			case "10":
				monthCreateWords = "Oct.";
				break;
			case "11":
				monthCreateWords = "Nov.";
				break;
			case "12":
				monthCreateWords = "Dec.";
				break;
		}
		switch(monthUpdate){
			case "1":
				monthUpdateWords = "Jan.";
				break;
			case "2":
				monthUpdateWords = "Feb.";
				break;
			case "3":
				monthUpdateWords = "Mar.";
				break;
			case "4":
				monthUpdateWords = "Apr.";
				break;
			case "5":
				monthUpdateWords = "May";
				break;
			case "6":
				monthUpdateWords = "Jun.";
				break;
			case "7":
				monthUpdateWords = "Jul.";
				break;
			case "8":
				monthUpdateWords = "Aug.";
				break;
			case "9":
				monthUpdateWords = "Sep.";
				break;
			case "10":
				monthUpdateWords = "Oct.";
				break;
			case "11":
				monthUpdateWords = "Nov.";
				break;
			case "12":
				monthUpdateWords = "Dec.";
				break;
		}
		$("#lstvw_Lists").append(
				'<li id="listitem"><a  onclick="toewijzen('
						+ row['ListNumber'] + ')"><h3 class="ui-li-heading">'
						+ row['ListName'] + ' - ' + yearCreate + ', ' +monthCreateWords + ' ' + dayCreate
						+ '</h3><p id="lists_changed' + row['ListNumber'] + '" style="display:inline;"></p><p style="display:inline;"> ' 
						+ yearUpdate + ', ' + monthUpdateWords + ' ' + dayUpdate
						+ '</p><p class="ui-li-count">' + row['aantal'] +'</p></a></li>');
		lijstenArray.push(row['ListName']);
		lijstenArrayMap[row['ListNumber']] = [ row['ListName']];
		$.i18n.properties({
			name:'messages',
			path:'bundle/',
			mode:'map',
			language:taal,
			callback: function(){
				$("#lists_changed"+ row['ListNumber']).text($.i18n.prop("msg_lbl_changed"));
			}
		});
	}
	var myScroll;
	myScroll = new iScroll('wrapper2', {bounce: false, hScrollbar: false, vScrollbar:true, vScroll:true});
	
	$("#lstvw_Lists").listview("refresh");
}
//de id toekennen zodanig dat je ze kan linken aan de lijstid per detail
function toewijzen(getal) {
	lijstid = getal;
	lijsttiteltest = lijstenArrayMap[lijstid];
	db.transaction(queryDetailDB, errorCB);
	$.mobile.loading("show", {
		theme:'a'
	});
	setTimeout(function(){
		$.mobile.changePage("#detailsVanLijst");
		$.mobile.loading("hide");
	}, 500);
}
function querySetting(tx){
	tx.executeSql("SELECT Value FROM Settings WHERE Key = 'ScanMode'",[], setScanRadioButtons, errorCB);
	function setScanRadioButtons(tx, result){
		keuze = result.rows.item(0)['Value'];
		switch (keuze){
		case '1':
			$("#optieCamera").prop('checked', true).checkboxradio("refresh");
			break;
		case '2':
			$("#optieManueel").prop('checked', true).checkboxradio("refresh");
			break;
		case '3':
			$("#optiecode").prop('checked', true).checkboxradio("refresh");
			break;
		case '4':
			$("#optieBluetooth").prop('checked', true).checkboxradio("refresh");
			break;
		}
	}
}
function querySortSetting(tx){
	tx.executeSql("SELECT Value FROM Settings WHERE Key = 'SortMode'",[], setSortRadioButtons, errorCB);
	function setSortRadioButtons(tx, result){
		sorteerkeuze = result.rows.item(0)['Value'];
		switch (sorteerkeuze){
		case '1':
			$("#sorteermake").prop('checked', true).checkboxradio("refresh");
			break;
		case '2':
			$("#sorteerpartnumber").prop('checked', true).checkboxradio("refresh");
			break;
		}
	}
}
//function queryLanguageSetting(tx){
//	tx.executeSql("SELECT Value FROM Settings WHERE Key = 'Taalinstelling'", [], setLanguageRadioButtons, errorCB);
//	function setLanguageRadioButtons(tx, result){
//		var taal = result.rows.item(0)['Value'];
//		if (taal==null){
//			navigator.globalization.getLocaleName(
//					function(locale){
//						taal = locale.value.substring(0,2);
//					},
//					function(){
//						taal="en";
//					});
//		}
//		switch(taal){
//		case 'nl':
//			$("#nl").prop("checked", true).checkboxradio("refresh");
//			break;
//		case 'fr':
//			$("#fr").prop("checked", true).checkboxradio("refresh");
//			break;
//		default:
//			$("#en").prop("checked", true).checkboxradio("refresh");
//		}
//		$("#" + taal).attr('checked', true).checkboxradio("refresh");
//		loadAndDisplayLanguage(taal);
//	}
//}
function queryItemsGemapt(tx){
	tx.executeSql("SELECT Key, Value FROM Settings WHERE Key='ItemMapping'", [], checkItemsMapped, errorCB);
	function checkItemsMapped(tx, result){
		if (result.rows.item(0)['Value']=='0'){
			$(".ui-footer, .ui-header").hide();
			$("#btn_ScanIntoList, #btn_ScanNewItem, #btn_UpdateItemInfo, #btn_DeleteItem").addClass('ui-disabled');
			$("<div>").simpledialog2({
				mode:"button",
				headerText: header_dialog_itemmapping,
				headerClose: false,
				buttonPrompt: msg_dialog_itemmapping,
				buttons: {
					"OK":{
						click:function(){
							$(".ui-footer, .ui-header").show();
							$("#btn_ScanIntoList, #btn_ScanNewItem, #btn_UpdateItemInfo, #btn_DeleteItem").removeClass('ui-disabled');
							db.transaction(selectAllItems, errorCB);
							$.mobile.changePage("#barcodemappinginvoer");
						},
						theme:"e"
					},
					"Cancel": {
						click:function(){
							$(".ui-footer, .ui-header").show();
							$.mobile.changePage("#main");
						},
						icon:"delete",
						theme:"c"
					}
				}
			});
		}
		else {
		}
	}
}
function dialogueRemoveList(){
	$(".ui-footer, .ui-header").hide();
	$("<div>").simpledialog2({
		mode:"button",
		headerText: header_dialog_deletelist,
		headerClose: false,
		buttonPrompt: msg_dialog_deletelist,
		buttons:{
			"OK": {
				click:function(){
					$(".ui-footer, .ui-header").show();
					db.transaction(deleteFromList, errorCB, successListCB);
					$.mobile.loading('show', {
						theme:'a'
					})
					setTimeout(function(){
						$.mobile.changePage("#mylists");
						$.mobile.loading('hide');
					}, 500);
				},
				theme:"e"
			},
			"Cancel":{
				click:function(){
					$(".ui-footer, .ui-header").show();
					$.mobile.changePage("#detailsVanLijst");
				},
				icon:"delete",
				theme:"c"
			}
		}
	});
}
// verwijdert lijsten uit de database
function deleteFromList(tx) {
	tx.executeSql("DELETE FROM ListDetail where ListHeader_ListNumber = ?",
			[ lijstid ]);
	tx.executeSql("DELETE FROM ListHeader where ListNumber=?", [ lijstid ]);
}
//verwijdert item uit lijst
function deleteFromDetail(tx) {
	tx.executeSql("DELETE FROM ListDetail where ListDetail_id = ?",
			[ detailid ]);
	var currentDate = new Date();
	var year = currentDate.getFullYear();
	var month = currentDate.getMonth() + 1;
	var day = currentDate.getDate();
	var datum = year + "-" + month + "-" + day;
	tx.executeSql("UPDATE ListHeader SET LastUpdated = ? WHERE ListNumber=?", [datum, lijstid], queryListDB, errorCB);
}
function successDetailsCB() {
	db.transaction(queryDetailDB, errorCB);
}
function queryDetailDB(tx) {
	tx.executeSql("SELECT ListName, ListHeader_ListNumber, ListDetail_id as detailid, barcode, NrOfItems, " +
				"MakeDescription, Partnumber, CustomerRef, Description " +
				"FROM ListHeader INNER JOIN " +
				"(ListDetail INNER JOIN (ItemMapping INNER JOIN CustomerMake ON Make = MakeCode) ON CustomerBarcode = barcode) " +
				"ON ListNumber = ListHeader_ListNumber WHERE ListNumber = ?", [lijstid], querySuccessDetail, errorCB);
}
//fill ul for DetailList
function querySuccessDetail(tx, result) {
	$("#lstvw_ListDetails").empty();
	barcodeArray = new Array();
	for (var i = 0; i<result.rows.length;i++){
		var row = result.rows.item(i);
		$("#lstvw_ListDetails").append(
				'<li text-overflow:clip><a onclick="opzoeken('
					+ row['detailid']
					+ ')"><h3 class="ul-li-heading">'
					+ row['MakeDescription']
					+ '-'
					+ row['Partnumber']
					+ '</h3><p>(Code: ' + row['barcode'] + ')</p><p class="ui-li-count">' + row['NrOfItems'] + '</p></a></li>');
			barcodeArray.push(row['barcode']);
	}
	$("#lijsttitel").text(lijsttiteltest);
	var myScroll;
	myScroll = new iScroll('wrapper3', {bounce: false, hScrollbar: false, vScrollbar:true, vScroll:true});
	$("#lstvw_ListDetails").listview("refresh");
}
//id van het detailorder opzoeken voor de detailinfo pagina
function opzoeken(getal) {
	detailid = getal;
	db.transaction(queryDetailInfo, errorCB);
	$.mobile.loading("show",{
		theme:'a'
	});
	setTimeout(function(){
		$.mobile.changePage("#detailInfo");
		$.mobile.loading("hide");
	}, 500);
}
function queryDetailInfo(tx) {
	tx.executeSql("SELECT ListNumber, ListDetail_id, barcode, NrOfItems, Make, MakeDescription, Partnumber, CustomerRef, Description FROM ListHeader INNER JOIN (ListDetail INNER JOIN (ItemMapping INNER JOIN CustomerMake ON Make = MakeCode) ON CustomerBarcode = barcode) ON ListNumber = ListHeader_ListNumber WHERE ListDetail_id = ?",
				[detailid], querySuccessDetailInfo, errorCB);
	// beter voor een single result
	function querySuccessDetailInfo(tx, result) {
		$("#detailinfo").text("");
		$("#detailinfo").append(
			'<tr><td id="detailinfo_partnumber"></td><td> ' + result.rows.item(0)['MakeDescription'] + '-'
				+ result.rows.item(0)['Partnumber'] + '</td></tr>');
		$("#detailinfo").append(
			'<tr><td id="detailinfo_barcode"></td><td> ' + result.rows.item(0)['barcode'] + '</td></tr>');
		$("#detailinfo").append(
			'<tr><td id="detailinfo_amount"></td><td> ' + result.rows.item(0)['NrOfItems'] + '</td></tr>');
		$("#detailinfo").append(
			'<tr><td id="detailinfo_list"></td><td> ' + lijsttiteltest + '</td></tr>');
		//$("#detailinfo").append('<br/><hr/>')
		if (result.rows.item(0)['CustomerRef'] != null) {
			$("#detailinfo").append(
				'<tr><td id="detailinfo_customerref"></td><td> ' + result.rows.item(0)['CustomerRef'] + '</td></tr>');
		}
		if (result.rows.item(0)['Description'] != null) {
			$("#detailinfo").append(
				'<tr><td id="detailinfo_description"></td><td> ' + result.rows.item(0)['Description'] + '</td></tr>');
		}
		$("#txt_BarcodeItemUpdate").val(result.rows.item(0)['barcode']);
		$("#slct_MakeUpdate").val(result.rows.item(0)['Make']);
		$("#txt_PartnumberUpdate").val(result.rows.item(0)['Partnumber']);
		$("#txt_CustomerRefUpdate").val(result.rows.item(0)['CustomerRef']);
		$("#DescriptionUpdate").val(result.rows.item(0)['Description']);
		barcodeVoorUpdate = result.rows.item(0)['barcode'];
		var myScroll;
		myScroll = new iScroll('wrapper4', {bounce:false, hScrollbar:false, vScrollbar:true, vScroll:true});
		var taal;
		navigator.globalization.getLocaleName(
				function(locale){
					taal = locale.value.substring(0,2);
				},
				function(){
					taal="en";
				});
		$.i18n.properties({
			name:'messages',
			path:'bundle/',
			mode:'map',
			language:taal,
			callback:function(){
				$("#detailinfo_partnumber").text($.i18n.prop("msg_lbl_codeinsert"));
				$("#detailinfo_barcode").text($.i18n.prop("msg_lbl_scanbarcode"));
				$("#detailinfo_amount").text($.i18n.prop("msg_lbl_scanamount"));
				$("#detailinfo_list").text($.i18n.prop("msg_lbl_list"));
				$("#detailinfo_customerref").text($.i18n.prop("msg_lbl_customerrefinsert"));
				$("#detailinfo_description").text($.i18n.prop("msg_lbl_description"));
			}
		});
	}
}
function addNewList(){
	lijstnaam = $("#txt_ListName").val();
	if(lijstnaam ==""){
		$("#popup_NewList").popup("open", {positionTo : $("#txt_ListName")});
	} else if (lijstenArray.some(isLijstnaamGelijkAan)){
		$("#popup_ListExists").popup("open", {positionTo : $("#txt_ListName")});
	} else {
		db.transaction(insertListInDB, errorCB, successDetailsCB);
		$.mobile.changePage("#detailsVanLijst");
		$("#txt_ListName").val("");
	}
}
function isLijstnaamGelijkAan(element, index, array) {
	return (element == lijstnaam);
}
function insertListInDB(tx) {
	tx.executeSql("INSERT INTO ListHeader(ListName) VALUES (?)", [ lijstnaam ], callNewListNumber, errorCB);
	function callNewListNumber(tx, result){
		lijstid = result.insertId;
		lijstenArray.push(lijstnaam);
		lijstenArrayMap[lijstid] = lijstnaam;
		lijsttiteltest = lijstnaam;
		$("#lijsttitel").text(lijsttiteltest);
	}
}
function addNewListDetail(){
	code = $("#txt_ScannedCode").val();
	aantal = $("#txt_ScannedAmount").val();
	if(code == ""){
		$("#popup_DetailEmpty").popup("open", {positionTo : $("#txt_ScannedCode")});
		$("#txt_ScannedCode").focus();
	} else if(aantal == ""|| isNaN(aantal)){
		$("#popup_DetailNotValid").popup("open", {positionTo : $("#txt_ScannedAmount")});
		$("#aantal").val("1");
		$("#aantal").focus();
	} else if (barcodeArray.some(barcodeEquals)){
		db.transaction(adjustAmount, errorCB);
		$("#txt_ScannedCode").val("");
		$("#txt_ScannedAmount").val("1");
		$.mobile.loading('show',{
			theme:'a'
		});
		setTimeout(function(){
			$.mobile.changePage('#detailsVanLijst');
			$.mobile.loading('hide');
		}, 500);
	} else{
		db.transaction(checkBarcodeInDB, errorCB);
	}
}
function barcodeEquals(element, index, array) {
	return (element == code);
}
function adjustAmount(tx){
	var currentDate = new Date();
	var year = currentDate.getFullYear();
	var month = currentDate.getMonth() + 1;
	var day = currentDate.getDate();
	var datum = year + "-" + month + "-" + day;
	tx.executeSql("UPDATE ListHeader SET LastUpdated = ? WHERE ListNumber=?", [datum, lijstid], queryListDB, errorCB);
	tx.executeSql("UPDATE ListDetail SET NrOfItems = NrOfItems + 1 WHERE barcode = ? AND ListHeader_ListNumber = ?", [code, lijstid], successDetailsCB, errorCB);
}
function checkBarcodeInDB(tx) {
	tx.executeSql("SELECT CustomerBarcode FROM ItemMapping WHERE CustomerBarcode = ?", [code], querySingleBarcode, errorCB);
	function querySingleBarcode(tx, result) {
		try {
			var currentDate = new Date();
			var year = currentDate.getFullYear();
			var month = currentDate.getMonth() + 1;
			var day = currentDate.getDate();
			var datum = year + "-" + month + "-" + day;
			tx.executeSql("UPDATE ListHeader SET LastUpdated = ? WHERE ListNumber = ?", [datum, lijstid], queryListDB, errorCB);
			tx.executeSql("INSERT INTO ListDetail(ListHeader_ListNumber, barcode, NrOfItems) VALUES (?,?,?)",
						[lijstid, result.rows.item(0)['CustomerBarcode'], aantal], successDetailsCB, errorCB);
			$.mobile.loading('show', {
				theme:'a'
			});
			setTimeout(function(){
				$.mobile.changePage("#detailsVanLijst");
				$("#txt_ScannedAmount").val("1");
				$("#txt_ScannedCode").val("");
				$.mobile.loading('hide');
			}, 500);
		} catch (e) {
			$(".ui-footer, .ui-header").hide();
			$("<div>").simpledialog2({
				mode:"button",
				headerText: header_itemnotexists,
				headerClose:false,
				buttonPrompt: dialog_itemnotexists,
				buttons:{
					"OK": {
						click: function(){
							$(".ui-footer, .ui-header").show();
							$("#txt_BarcodeInsertSAYG").val($("#txt_ScannedCode").val());
							$.mobile.changePage("#scanasyougo");
						},
						theme:"e"
					},
					"Cancel":{
						click:function(){
							$(".ui-footer, .ui-header").show();
							$("#txt_ScannedAmount").val("1");
							$("#txt_ScannedCode").val("");
							$.mobile.changePage("#detailsVanLijst");
						},
						icon:"delete",
						theme:"c"
					}
				}			
			});		
		}
	}
}
function updateDetailSubmit(event) {
	event.preventDefault();
	detailUpdate();
}
function detailUpdate(){
	var aantalUpdate = $("#txt_AmountUpdated").val();
	if(isNaN(aantalUpdate)||aantalUpdate <= 0 ){
		$("#popup_Update").popup("open", {positionTo : $("#txt_AmountUpdated")});
		$("#txt_AmountUpdated").focus();
	} else {
		db.transaction(updateDetail, errorCB, successDetailsCB);
		$.mobile.loading('show', {
			theme:'a'
		});
		setTimeout(function(){
			$.mobile.changePage("#detailsVanLijst");
			$.mobile.loading('hide');
		}, 500);
	}
}
function updateDetail(tx) {
	var currentDate = new Date();
	var year = currentDate.getFullYear();
	var month = currentDate.getMonth() + 1;
	var day = currentDate.getDate();
	var datum = year + "-" + month + "-" + day;
	tx.executeSql("UPDATE ListHeader SET LastUpdated = ? WHERE ListNumber = ?", [datum, lijstid], queryListDB, errorCB);
	tx.executeSql(
			"UPDATE ListDetail SET NrOfItems = ? where ListDetail_id = ?", [
			$("#txt_AmountUpdated").val(), detailid ]);
}
function insertNewTVHCode(){
	var make = ("#slct_MakeInsert").val();
	var tvhcode = $("#txt_TVHCodeInsert").val();
	var tvhcodeaantal = $("#txt_TVHCodeAmount").val();
	if(tvhcode == ""){
		$("#popup_CodeEmpty").popup("open", {positionTo:$("#txt_TVHCodeInsert")});
		$("#txt_TVHCodeInsert").focus();
	/*} else if (isNaN(tvhcodeaantal) || tvhcodeaantal <= 0) {
		$("#popup_CodeNotValid").popup("open", {positionTo:$("#txt_TVHCodeAmount")});
		$("#txt_TVHCodeAmount").val("");
		$("#txt_TVHCodeAmount").focus();*/
	} else {
		db.transaction(checkTVHCodeInDB, errorCB);
	}
}
function checkTVHCodeInDB(tx){
	tx.executeSql("SELECT * FROM ItemMapping WHERE Partnumber = ? AND Make = ?", [$("#txt_TVHCodeInsert").val(), $("#slct_MakeInsert").val()], 
			querySingleTVHCode, errorCB);
	function querySingleTVHCode(tx, result){
		try{
			var currentDate = new Date();
			var year = currentDate.getFullYear();
			var month = currentDate.getMonth() + 1;
			var day = currentDate.getDate();
			var datum = year + "-" + month + "-" + day;
			tx.executeSql("UPDATE ListHeader SET LastUpdated = ? WHERE ListNumber = ?", [datum, lijstid], queryListDB, errorCB);
			tx.executeSql(
					"INSERT INTO ListDetail(ListHeader_ListNumber, barcode, NrOfItems) VALUES (?,?,?)",
					[lijstid, result.rows.item(0)['CustomerBarcode'], $("#txt_TVHCodeAmount").val()], successDetailsCB, errorCB);
			$.mobile.loading('show', {
				theme:'a'
			});
			setTimeout(function(){
				$.mobile.changePage("#detailsVanLijst");
				$.mobile.loading('hide');
			}, 500);
			$("#txt_TVHCodeInsert", "#txt_TVHCodeAmount").val("");
		} catch(e){
			$("#popup_CodeFail").popup("open", {positionTo:$("#txt_TVHCodeInsert")});
			$("#txt_TVHCodeInsert").val("");
		}
	}
}
function insertItemMapping(tx) {
	code = $("#txt_BarcodeInsert").val();
	var make = $("#slct_Make").val();
	var partnumber = $("#txt_PartnumberInsert").val();
	var customerRef = $("#txt_CustomerRefInsert").val();
	if (customerRef == "") {
		customerRef = null;
	}
	tx.executeSql("SELECT CustomerBarcode FROM ItemMapping WHERE CustomerBarcode = ?", [code], checkIfBarcodeInItemmapping, errorCB);
	function checkIfBarcodeInItemmapping(tx, result){
		if(result.rows.length>0){
			$(".ui-footer, .ui-header").hide();
			$("<div>").simpledialog2({
				mode:"button",
				headerText:header_itemexists,
				headerClose:false,
				buttonPrompt: dialog_itemexists,
				buttons:{
					"OK": {
						click: function(){
							$(".ui-footer, .ui-header").show();
							tx.executeSql("UPDATE ItemMapping SET Make = ?, Partnumber = ?, CustomerRef = ?", 
									[make, partnumber, customerRef]);
						},
						theme:"e"
					},
					"Cancel":{
						click:function(){
							$(".ui-header, .ui-footer").show();
							$.mobile.changePage("#items");
						},
						icon:"delete",
						theme:"c"
					}
				}
			})
		}
		else {
			tx.executeSql("INSERT INTO ItemMapping(Make, Partnumber, CustomerRef, CustomerBarcode, New) VALUES (?,?,?,?,1)", 
					[make, partnumber, customerRef, code]);
		}
	}
	$("#slct_Make, #txt_PartnumberInsert, #txt_CustomerRefInsert").val("");
}	
function updateItemMapping(tx) {
	var customerref;
	var description;
	if ($("#txt_CustomerRefUpdate").val() == "") {
		customerref = null;
	} else {
		customerref = $("#txt_CustomerRefUpdate").val();
	}
	tx.executeSql("UPDATE ItemMapping SET Make = ?, Partnumber = ?, CustomerRef = ? WHERE CustomerBarcode = ?",
				[$('#slct_MakeUpdate').val(), $('#txt_PartnumberUpdate').val(),
				 customerref, $("#txt_BarcodeItemUpdate").val()]);
	db.transaction(queryItemDetail, errorCB);
}
function queryItemDetail(tx){
	tx.executeSql("SELECT * FROM ItemMapping INNER JOIN CustomerMake ON Make = MakeCode WHERE CustomerBarcode = ?", [itemBarcode], querySuccessItemDetail, errorCB);
	function querySuccessItemDetail(tx, result){
		var taal;
		navigator.globalization.getLocaleName(
				function(locale){
					taal = locale.value.substring(0,2);
				},
				function(){
					taal="en";
				});
		if (result.rows.length>0){
			$("#iteminfo").text("");
			$("#iteminfo").append("<tr><td id='itemdetail_partnumbercode'></td><td> " + result.rows.item(0)['MakeDescription'] + "-" + result.rows.item(0)['Partnumber'] + "</td></tr>");
			$("#iteminfo").append("<tr><td id='itemdetail_barcode'></td><td> " + result.rows.item(0)['CustomerBarcode'] + "</td></tr>");
			if(result.rows.item(0)['CustomerRef']!=null){
				$("#iteminfo").append("<tr><td id='itemdetail_customerref'></td><td> " + result.rows.item(0)['CustomerRef'] + "</td></tr>");			
			}
			if(result.rows.item(0)['Description']!=null){
				$("#iteminfo").append("<tr><td id='itemdetail_description'></td><td> " + result.rows.item(0)['Description'] + "</td></tr>");			
			}
			$("#txt_BarcodeItemUpdate").val(result.rows.item(0)['CustomerBarcode']);
			$("#slct_MakeUpdate").val(result.rows.item(0)['MakeCode']);
			$("#txt_PartnumberUpdate").val(result.rows.item(0)['Partnumber']);
			$("#txt_CustomerRefUpdate").val(result.rows.item(0)['CustomerRef']);
			var myScroll;
			myScroll = new iScroll('wrapper5', {bounce:false, hScrollbar:false, vScrollbar:true, vScroll:true});
			$.i18n.properties({
				name:'messages',
				path:'bundle/',
				mode:'map',
				language: taal,
				callback:function(){
					$("#itemdetail_partnumbercode").text($.i18n.prop("msg_lbl_codeinsert"));
					$("#itemdetail_barcode").text($.i18n.prop("msg_lbl_scanbarcode"));
					$("#itemdetail_customerref").text($.i18n.prop("msg_lbl_customerrefinsert"));
					$("#itemdetail_description").text($.i18n.prop("msg_lbl_description"));
				}
			});
		}
		else{
			$("#iteminfo").text("");
			$("#iteminfo").append("<tr><td id='itemdetail_nomapping'></td></tr>");
			$.i18n.properties({
				name:'messages',
				path:'bundle/',
				mode:'map',
				language:taal,
				callback:function(){
					$("#itemdetail_nomapping").text($.i18n.prop("msg_lbl_nomapping"));
				}
			});
		}
	}
}
function selectAllItems(tx){
	tx.executeSql("SELECT Make, MakeDescription, Partnumber FROM ItemMapping INNER JOIN CustomerMake ON Make = MakeCode ORDER BY Partnumber",[], mapAllItems, errorCB);
	function mapAllItems(tx, result){
		itemMap = new Array();
		itemArrayUpdated=new Array();
		for (var i=0; i<result.rows.length; i++){
			var row=result.rows.item(i);
			itemMap.push([row['Make'], row['Partnumber'], row['MakeDescription']]);
		}
		$("#codedisplay").append(itemMap[0][2] + "-" + itemMap[0][1]);
		$("#codedisplaymanueel").append(itemMap[0][2] + "-" + itemMap[0][1]);
		itemMapIndex = 0;
	}
}
function deleteFromItemMapping(tx){
	tx.executeSql("SELECT COUNT(*) AS aantal FROM ListDetail WHERE barcode = ?", [itemBarcode], itemIsInLists, errorCB);
	function itemIsInLists(tx, result){
		var aantalLijsten = result.rows.item(0)['aantal'];
		if(aantalLijsten==0){
			$(".ui-footer, .ui-header").hide();
			$("<div>").simpledialog2({
				mode:"button",
				headerText:header_deletefrommapping,
				headerClose: false,
				buttonPrompt: dialog_deletefrommapping,
				buttons: {
					"OK": {
						click: function(){
							$(".ui-footer, .ui-header").show();
							db.transaction(deleteFromMappingFinal, errorCB);
							$.mobile.loading("show", {
								theme:'a'
							});
							setTimeout(function(){
								$.mobile.changePage("#items");
								$.mobile.loading('hide');
							}, 500);
							$("#popup_ItemRemoved").popup("open");
						},
						theme:"e"
					},
					"Cancel":{
						click:function(){
							$(".ui-footer, .ui-header").show();
							$.mobile.changePage("#itemdetail");
						},
						icon:"delete",
						theme:"c"
					}
				}
			});
		}
		else {
			$(".ui-footer, .ui-header").hide();
			$("<div>").simpledialog2({
				mode:"button",
				headerText: header_presentP1 + ' ' + aantalLijsten + ' ' + header_presentP2,
				headerClose:false,
				buttonPrompt: dialog_removefrommapping,
				buttons:{
					"OK": {
						click: function(){
							$(".ui-footer, .ui-header").show();
							db.transaction(deleteFromMappingFinal, errorCB);
							$.mobile.loading('show', {
								theme:'a'
							});
							setTimeout(function(){
								$.mobile.changePage('#items');
								$.mobile.loading('hide');
							}, 500);
							$("#popup_ItemRemoved").popup("open");
						},
						theme:"e"
					},
					"Cancel":{
						click:function(){
							$(".ui-footer, .ui-header").show();
							$.mobile.changePage("#itemdetail");
						},
						icon:"delete",
						theme:"c"
					}
				}			
			});	
		}
	}
}
function deleteFromMappingFinal(tx){
	tx.executeSql("DELETE FROM ItemMapping WHERE CustomerBarcode = ?", [itemBarcode]);
	tx.executeSql("DELETE FROM ListDetail WHERE barcode = ?", [itemBarcode]);
}
function adjustMapping(tx){
	for (var i=0; i<itemArrayUpdated.length;i++){
		var mappingMakeCode = itemArrayUpdated[i][0];
		var mappingPartnumber = itemArrayUpdated[i][1];
		var mappingEigenBarcode = itemArrayUpdated[i][2];
		tx.executeSql("UPDATE ItemMapping SET CustomerBarcode = ? WHERE Partnumber = ? AND Make = ?", 
					[mappingEigenBarcode, mappingPartnumber, mappingMakeCode]);
	}
	tx.executeSql("UPDATE Settings SET Value = 1 WHERE Key = ?", ["ItemMapping"]);
}
function setScanOption(tx){
	if($("#optieCamera").is(':checked') == true){
		tx.executeSql("UPDATE Settings SET Value = 1 WHERE Key = 'ScanMode'");
		keuze = 1;
	} else if ($("#optieManueel").is(':checked') == true) {
		tx.executeSql("UPDATE Settings SET Value = 2 WHERE Key = 'ScanMode'");
		keuze = 2;
	} else if($("#optiecode").is(':checked') == true){
		tx.executeSql("UPDATE Settings SET Value = 3 WHERE Key = 'ScanMode'");
		keuze = 3;
	} else{
		tx.executeSql("UPDATE Settings SET Value = 4 WHERE Key = 'ScanMode'");
		keuze = 4;
	}
}
function setSort(tx){
	if($("#sorteermake").is(':checked') == true){
		tx.executeSql("UPDATE Settings SET Value = 1 WHERE Key = 'SortMode'");
		sorteerkeuze = 1;
	} else {
		tx.executeSql("UPDATE Settings SET Value = 2 WHERE Key = 'SortMode'");
		sorteerkeuze = 2;
	} 
}
/*function setLanguage(tx){
	var taal = $('input[name=taalkeuze]:checked').val();
	tx.executeSql("UPDATE Settings SET Value = ? WHERE Key = 'Taalinstelling'", [taal]);
	$.mobile.changePage("#settings");
	loadAndDisplayLanguage(taal);
	$("<div>").simpledialog2({
		mode:"button",
		headerText: $.i18n.properties({name:"messages", path:"bundle/", mode:"map", language:taal, callback: function(){$.i18n.prop("msg_language_changed");}}),
		headerClose:false,
		buttonPrompt: "Your application will now be closed.  Please restart to implement changes.",
		buttons:{
			"OK":{
				click:function(){
					//navigator.app.exitApp();
				}
			}
		}
	});
}*/
function checkDataExists(tx){
	tx.executeSql("SELECT Key, Value FROM Settings WHERE Key = 'UserData'", [], pickRightPage, errorCB);
	function pickRightPage(tx, result){
		if (result.rows.item(0)['Value'] == '0'){
			$.mobile.changePage("#gegevens");
		} else {
			$.mobile.changePage("#gegevensweergave");
		}
	}
}
function insertUserData(tx){
	tx.executeSql("INSERT INTO Gegevens (Naam, Bedrijf, Paswoord, Klantnummer) VALUES (?,?,?,?)", [$("#txt_UserName").val(), $("#txt_Company").val(), $("#txt_Password").val(), $("#txt_ClientCode").val()]);
	tx.executeSql("UPDATE Settings SET Value = 1 WHERE Key = 'UserData'");
	$("#txt_UserName, #txt_Company, #txt_Password").val("");
}
function queryItemsDB(tx){
	if(sorteerkeuze == 1){
		tx.executeSql("SELECT * FROM ItemMapping INNER JOIN CustomerMake ON Make = MakeCode ORDER BY MakeDescription", [], querySuccessItems, errorCB);
	} else {
		tx.executeSql("SELECT * FROM ItemMapping INNER JOIN CustomerMake ON Make = MakeCode ORDER BY Partnumber", [], querySuccessItems, errorCB);
	} 
}
function querySuccessItems(tx, result){
	$("#lstvw_Items").empty();
	for (var i=0; i< result.rows.length;i++){
		var row = result.rows.item(i);
		$("#lstvw_Items").append(
				'<li><a onclick="itemToekennen(' + row['CustomerBarcode'] + ')">'
				+ '<h3 class="ul-li-heading">' + row['MakeDescription'] + '-' + row['Partnumber'] + '</h3></a></li>');
	}
	$("#lstvw_Items").listview("refresh");
	var myScroll;
	myScroll = new iScroll('wrapper1', {bounce: false, hScrollbar: false, vScrollbar:true, vScroll:true});
}
function itemToekennen(getal){
	itemBarcode = getal;	
	$.mobile.loading("show", {
		theme:'a'
	});
	setTimeout(function(){
		$.mobile.changePage("#itemdetail");
		$.mobile.loading("hide");
	}, 500);
}
function queryUserData(tx){
	tx.executeSql("SELECT Naam, Bedrijf, Klantnummer FROM Gegevens", [], displayUserData, errorCB);
	function displayUserData(tx, result){
		$("#gebruikersinfo").text("");
		$("#gebruikersinfo").append('<br/>');
		$("#gebruikersinfo").append('<strong><p id="data_username" style="display:inline;"></p></strong> ' + result.rows.item(0)['Naam']);
		$("#gebruikersinfo").append('<br/><br/><strong><p id="data_company" style="display:inline;"></p></strong> ' + result.rows.item(0)['Bedrijf']);
		$("#gebruikersinfo").append('<br/><br/>');
		$("#gebruikersinfo").append('<strong><p id="data_clientcode" style="display:inline;"></p></strong> ' + result.rows.item(0)['Klantnummer']);
		var taal;
		navigator.globalization.getLocaleName(
				function(locale){
					taal = locale.value.substring(0,2);
				},
				function(){
					taal="en";
				});
		$.i18n.properties({
			name:'messages',
			path:'bundle/',
			mode:'map',
			language:taal,
			callback: function(){
				$("#data_username").text($.i18n.prop("msg_lbl_UserName"));
				$("#data_company").text($.i18n.prop("msg_lbl_Company"));
				$("#data_clientcode").text($.i18n.prop("msg_lbl_ClientCode"));
			}
		});
	}
}
function insertItemMappingSAYG(tx) {
	var make = $("#slct_MakeInsertSAYG").val();
	var partnumber = $("#txt_PartnumberInsertSAYG").val();
	var customerRef = $("#txt_CustomerRefInsertSAYG").val();
	if (customerRef == "") {
		customerRef = null;
	}
	tx.executeSql("INSERT INTO ItemMapping(CustomerBarcode, Make, Partnumber, CustomerRef) VALUES (?,?,?,?)",
		[code, make, partnumber, customerRef]);
	$("#slct_MakeInsertSAYG, #txt_PartnumberInsertSAYG, #txt_CustomerRefInsertSAYG").val("");
}
function exportCall(tx){
	tx.executeSql("SELECT * FROM ListDetail WHERE ListDetail_id = ? ", [detailid], fillDetails, errorCB);
	function fillDetails(tx, result){
		$("#barcodevoorexport").empty();
		$("#barcodevoorexport").append(result.rows.item(0)['barcode']);
		$("#txt_ExportAmount").val(result.rows.item(0)['NrOfItems']);
	}
	tx.executeSql("SELECT * FROM ListHeader",[], fillListSelector, errorCB);
	function fillListSelector(tx, result){
		$("#slct_Lists").empty();
		$("#slct_Lists").append('<option>Choose one...</option>');
		for (var i=0; i<result.rows.length;i++){
			var row = result.rows.item(i);
			if(row['ListNumber']!=lijstid){
				$("#slct_Lists").append(
						'<option value="' + row['ListNumber'] + '">'
						+ row['ListName'] + '</option>');
			}
		}
		$("#slct_Lists").selectmenu('refresh');
	}
}
function exportToOtherList(tx){
	var lijsthoofd = $("#slct_Lists").val();
	var barcodeVoorExport = $("#barcodevoorexport").text();
	var aantalVoorExport = $("#txt_ExportAmount").val();
	tx.executeSql("SELECT * FROM ListDetail WHERE ListHeader_ListNumber = ? AND barcode = ?", [lijsthoofd, barcodeVoorExport], exportedItemAlreadyInList, errorCB);
	function exportedItemAlreadyInList(tx, result){
		if (result.rows.length == 0){
			tx.executeSql("INSERT INTO ListDetail(ListHeader_ListNumber, barcode, NrOfItems) VALUES (?,?,?)", [lijsthoofd, barcodeVoorExport, aantalVoorExport]);
		}
		else {
			tx.executeSql("UPDATE ListDetail SET NrOfItems = ? WHERE ListHeader_ListNumber = ? AND barcode = ?", [aantalVoorExport, lijsthoofd, barcodeVoorExport]);
			$("#popup_Export").popup("open", {positionTo:$("#txt_ExportAmount")});
		}
	}
	var currentDate = new Date();
	var year = currentDate.getFullYear();
	var month = currentDate.getMonth() + 1;
	var day = currentDate.getDate();
	var datum = year + "-" + month + "-" + day;
	tx.executeSql("UPDATE ListHeader SET LastUpdated = ? WHERE ListNumber = ?", [datum,lijsthoofd], queryListDB, errorCB);
}
function createMakeTable(tx){
	tx.executeSql("DROP TABLE IF EXISTS CustomerMake");
	tx.executeSql("CREATE TABLE IF NOT EXISTS CustomerMake (MakeCode TEXT PRIMARY KEY, MakeDescription TEXT)");
	for (var i=0; i < makelist.length; i++){
		tx.executeSql("INSERT INTO CustomerMake (MakeCode, MakeDescription) VALUES (?,?)", [makelist[i][0], makelist[i][1]]);
	}
}
function loadAndDisplayLanguage(lang){
	$.i18n.properties({
		name:'messages',
		path:'bundle/',
		mode:'map',
		language:lang,
		callback: function(){
					
			$("#header_main").text($.i18n.prop("msg_main_header"));
			$("#header_mylists").text($.i18n.prop("msg_mylists_header"));
			$("#header_detail").text($.i18n.prop("msg_detail_header"));
			$("#header_NewList").text($.i18n.prop("msg_newlist_header"));
			$("#header_Scan").text($.i18n.prop("msg_scan_header"));
			$("#header_UpdateDetail").text($.i18n.prop("msg_UpdateDetail_header"));
			$("#header_InsertTVHCode").text($.i18n.prop("msg_InsertTVHCode_header"));
			$("#header_NewItem").text($.i18n.prop("msg_InsertTVHCode_header"));
			$("#header_itemupdate").text($.i18n.prop("msg_itemupdate_header"));
			$("#header_ItemInfo").text($.i18n.prop("msg_detail_header"));
			$("#header_MappingInsert").text($.i18n.prop("msg_footer_itemmapping"));
			$("#header_Mapping").text($.i18n.prop("msg_barcodemapping_header"));
			$("#header_MappingList").text($.i18n.prop("msg_barcodemapping_header"));
			$("#header_settings").text($.i18n.prop("msg_footer_settings"));
			$("#header_scansettings").text($.i18n.prop("msg_scansettings_header"));
			$("#header_Sort").text($.i18n.prop("msg_Sort_header"));
			$("#header_UserData").text($.i18n.prop("msg_UserData_header"));
			$("#header_UserDataInfo").text($.i18n.prop("msg_UserData_header"));
			$("#header_scanasyougo").text($.i18n.prop("msg_InsertTVHCode_header"));
			$("#header_export").text($.i18n.prop("msg_export_header"));
			$("#header_MappingManually").text($.i18n.prop("msg_footer_itemmapping"));
			$("#header_LanguageSetting").text($.i18n.prop("msg_SetLanguage"));
						
			$("#popup_NewList").text($.i18n.prop("msg_popupNewList"));
			$("#popup_ListExists").text($.i18n.prop("msg_popupNewListExixts"));
			$("#popup_DetailEmpty").text($.i18n.prop("msg_popupNewDetailEmpty"));
			$("#popup_DetailNotValid").text($.i18n.prop("msg_popupUpdate"));
			$("#popup_Update").text($.i18n.prop("msg_popupUpdate"));
			$("#popup_CodeEmpty").text($.i18n.prop("msg_popupCodeEmpty"));
			$("#popup_CodeNotValid").text($.i18n.prop("msg_popupUpdate"));
			$("#popup_CodeFail").text($.i18n.prop("msg_popupExistsNot"));
			$("#popup_Make").text($.i18n.prop("msg_popupMake"));
			$("#popup_Partnumber").text($.i18n.prop("msg_popupPartnumber"));
			$("#popup_MakeUpdate").text($.i18n.prop("msg_popupMake"));
			$("#popup_PartnumberUpdate").text($.i18n.prop("msg_popupPartnumber"));
			$("#popup_ItemRemoved").text($.i18n.prop("msg_popupItemRemoved"));
			$("#popup_MappingEmpty").text($.i18n.prop("msg_popupBarcodeForMappingEmpty"));
			$("#popup_ScanUpdate").text($.i18n.prop("msg_popupScanSettingUpdated"));
			$("#popup_SortUpdate").text($.i18n.prop("msg_popupSortSettingUpdated"));
			$("#popup_Username").text($.i18n.prop("msg_popupEmptyUsername"));
			$("#popup_Company").text($.i18n.prop("msg_popupEmptyCompanyname"));
			$("#popup_Password").text($.i18n.prop("msg_popupEmptyPassword"));
			$("#popup_ClientCode").text($.i18n.prop("msg_popupEmptyClientCode"));
			$("#popup_MakeSAYG").text($.i18n.prop("msg_popupMake"));
			$("#popup_PartnumberSAYG").text($.i18n.prop("msg_popupPartnumber"));
			$("#popup_Export").text($.i18n.prop("msg_popupExportItem"));
			$("#popup_MappingManuallyEmpty").text($.i18n.prop("msg_popupBarcodeForMappingEmpty"));
						
			$("#lbl_ListName").text($.i18n.prop("msg_lbl_listname"));
			$("#lbl_ScanBarcode").text($.i18n.prop("msg_lbl_scanbarcode"));
			$("#lbl_ScanAmount").text($.i18n.prop("msg_lbl_scanamount"));
			$("#lbl_barcodeupdate").text($.i18n.prop("msg_lbl_scanbarcode"));
			$("#lbl_UpdateAmount").text($.i18n.prop("msg_lbl_scanamount"));
			$("#lbl_MakeSelect").text($.i18n.prop("msg_lbl_makeinsertselect"));
			$("#lbl_CodeInsert").text($.i18n.prop("msg_lbl_codeinsert"));
			$("#lbl_CodeAmount").text($.i18n.prop("msg_lbl_scanamount"));
			$("#lbl_barcodeinsert").text($.i18n.prop("msg_lbl_scanbarcode"));
			$("#lbl_makeinsert").text($.i18n.prop("msg_lbl_makeinsertselect"));
			$("#lbl_partnumberinsert").text($.i18n.prop("msg_lbl_codeinsert"));
			$("#lbl_customerrefinsert").text($.i18n.prop("msg_lbl_customerrefinsert"));
			$("#lbl_barcodeitemupdate").text($.i18n.prop("msg_lbl_scanbarcode"));
			$("#lbl_makeupdate").text($.i18n.prop("msg_lbl_makeinsertselect"));
			$("#lbl_partnumberupdate").text($.i18n.prop("msg_lbl_codeinsert"));
			$("#lbl_customerrefupdate").text($.i18n.prop("msg_lbl_customerrefinsert"));
			$("#lbl_smartphonecamera").text($.i18n.prop("msg_lbl_smartphonecamera"));
			$("#lbl_Manual").text($.i18n.prop("msg_lbl_optionmanually"));
			$("#lbl_optioncode").text($.i18n.prop("msg_lbl_optioncode"));
			$("#lbl_optionbluetooth").text($.i18n.prop("msg_lbl_optionbluetooth"));
			$("#lbl_MakeSort").text($.i18n.prop("msg_lbl_SortMake"));
			$("#lbl_PartnumberSort").text($.i18n.prop("msg_lbl_SortPartnumber"));
			$("#lbl_nl").text($.i18n.prop("msg_lbl_nl"));
			$("#lbl_fr").text($.i18n.prop("msg_lbl_fr"));
			$("#lbl_en").text($.i18n.prop("msg_lbl_en"));
			$("#lbl_Username").text($.i18n.prop("msg_lbl_UserName"));
			$("#lbl_Company").text($.i18n.prop("msg_lbl_Company"));
			$("#lbl_ClientCode").text($.i18n.prop("msg_lbl_ClientCode"));
			$("#lbl_Password").text($.i18n.prop("msg_lbl_Password"));
			$("#lbl_barcodeinsertsayg").text($.i18n.prop("msg_lbl_scanbarcode"));
			$("#lbl_makeinsertsayg").text($.i18n.prop("msg_lbl_makeinsertselect"));
			$("#lbl_partnumbersayg").text($.i18n.prop("msg_lbl_codeinsert"));
			$("#lbl_customerrefinsertsayg").text($.i18n.prop("msg_lbl_customerrefinsert"));
			$("#lbl_ExportAmount").text($.i18n.prop("msg_lbl_scanamount"));
			$("#lbl_ListSelect").text($.i18n.prop("msg_lbl_ListsSelect"));
						
			$("#greetingP1").text($.i18n.prop("msg_greetingP1"));
			$("#greetingP2").text($.i18n.prop("msg_greetingP2"));
			$("#greetingP3").text($.i18n.prop("msg_greetingP3"));
			$("#greetingP4").text($.i18n.prop("msg_greetingP4"));
			$("#lgnd_Sort").text($.i18n.prop("msg_lgnd_Sort"));
			$("#lgnd_scansetting").text($.i18n.prop("msg_lgnd_scansetting"));					
			$("#lgnd_Language").text($.i18n.prop("msg_lgnd_Language"));
			$(".ui-btn-left").text($.i18n.prop("msg_backbutton"));
			quitText = $.i18n.prop("msg_quit");
			quitHeader = $.i18n.prop("msg_hdr_quit");
			msg_dialog_DeleteDetail = $.i18n.prop("msg_DeleteDetail");
			header_dialog_DeleteDetail = $.i18n.prop("msg_header_DeleteDetail");
			header_dialog_itemmapping = $.i18n.prop("msg_itemmapping");
			msg_dialog_itemmapping = $.i18n.prop("msg_dialog_mapping");
			header_dialog_deletelist = $.i18n.prop("msg_deletedialogheader");
			msg_dialog_deletelist = $.i18n.prop("msg_deletelist");
			header_itemnotexists = $.i18n.prop("msg_header_itemnotexists");
			dialog_itemnotexists = $.i18n.prop("msg_dialog_itemnotexists");
			header_itemexists = $.i18n.prop("msg_header_itemexists");
			dialog_itemexists = $.i18n.prop("msg_dialog_itemexists");
			header_deletefrommapping = $.i18n.prop("msg_header_deletefrommapping");
			dialog_deletefrommapping = $.i18n.prop("msg_dialog_deletefrommapping");
			dialog_removefrommapping = $.i18n.prop("msg_dialog_removefrommapping");
			loadingText = $.i18n.prop("msg_downloading");
			header_dialog_downloadOK = $.i18n.prop("msg_downloadcomplete");
			header_dialog_downloadfail = $.i18n.prop("msg_downloadfail");
			header_presentP1 = $.i18n.prop("msg_header_presentP1");
			header_presentP2 = $.i18n.prop("msg_header_presentP2");
			$(".footer_main").text($.i18n.prop("msg_footer_main"));
			$(".footer_mylists").text($.i18n.prop("msg_footer_mylists"));
			$(".footer_mapping").text($.i18n.prop("msg_footer_itemmapping"));
			$(".footer_Settings").text($.i18n.prop("msg_footer_settings"));
						
			$("#btn_NewList").text($.i18n.prop("msg_NewListButton"));
			$("#btn_ScanIntoList").text($.i18n.prop("msg_scan"));
			$("#btn_RemoveList").text($.i18n.prop("msg_remove"));
			$("#btn_SendList").text($.i18n.prop("msg_Send"));
			$("#btn_UpdateDetail").text($.i18n.prop("msg_UpdateItem"));
			$("#btn_DeleteDetail").text($.i18n.prop("msg_RemoveDetail"));
			$("#btn_ExportDetail").text($.i18n.prop("msg_ExportDetail"));
			$("#btn_CreateNewList").text($.i18n.prop("msg_save"));
			$("#btn_NewDetail").text($.i18n.prop("msg_save"));
			$("#btn_UpdateDetailConfirm").text($.i18n.prop("msg_save"));
			$("#btn_NewDetailEntry").text($.i18n.prop("msg_save"));
			$("#btn_NewItemFromSAYG").text($.i18n.prop("msg_save"));
			$("#btn_UpdateItem").text($.i18n.prop("msg_save"));
			$("#btn_ScanNewItem").text($.i18n.prop("msg_scanButton2"));
			$("#btn_UpdateItemInfo").text($.i18n.prop("msg_update"));
			$("#btn_DeleteItem").text($.i18n.prop("msg_delete"));
			$("#btn_NextBarcode").text($.i18n.prop("msg_next"));
			$("#btn_ConfirmMapping").text($.i18n.prop("msg_save"));
			$("#btn_UserData").text($.i18n.prop("msg_UserData"));
			$("#btn_setscanmode").text($.i18n.prop("msg_btn_setscanmode"));
			$("#btn_SortMapping").text($.i18n.prop("msg_SortMapping"));
			$("#btn_SetLanguage").text($.i18n.prop("msg_SetLanguage"));
			$("#btn_GetMakeList").text($.i18n.prop("msg_catchMakeList"));
			$("#btn_SetScanOption").text($.i18n.prop("msg_save"));
			$("#btn_SetSortOption").text($.i18n.prop("msg_save"));
			$("#btn_SetLanguageOption").text($.i18n.prop("msg_save"));
			$("#btn_SetUserData").text($.i18n.prop("msg_save"));
			$("#btn_ConfirmItemSAYG").text($.i18n.prop("msg_save"));
			$("#btn_ConfirmExport").text($.i18n.prop("msg_save"));
			$("#btn_NextBarcodeManually").text($.i18n.prop("msg_next"));
		}			
				
	});
}