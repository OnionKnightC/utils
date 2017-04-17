```java
import java.io.IOException;
import java.io.InputStream;
import java.util.LinkedList;

import javax.xml.parsers.ParserConfigurationException;

import org.apache.poi.openxml4j.exceptions.OpenXML4JException;
import org.apache.poi.openxml4j.opc.OPCPackage;
import org.apache.poi.openxml4j.opc.PackageAccess;
import org.apache.poi.util.SAXHelper;
import org.apache.poi.xssf.eventusermodel.ReadOnlySharedStringsTable;
import org.apache.poi.xssf.eventusermodel.XSSFReader;
import org.apache.poi.xssf.eventusermodel.XSSFSheetXMLHandler;
import org.apache.poi.xssf.eventusermodel.XSSFSheetXMLHandler.SheetContentsHandler;
import org.apache.poi.xssf.model.StylesTable;
import org.xml.sax.InputSource;
import org.xml.sax.SAXException;
import org.xml.sax.XMLReader;

public class POIUtil{
    private LinkedList<LinkedList<String>> context = new LinkedList<LinkedList<String>>();
    
    public void process(String excelFilePath, String ... sheetNames) throws IOException, SAXException, ParserConfigurationException, OpenXML4JException{
        context.clear();
        
        OPCPackage pkg = OPCPackage.open(excelFilePath, PackageAccess.READ);
        XSSFReader xssfReader = new XSSFReader(pkg);
        StylesTable styles = xssfReader.getStylesTable();
        ReadOnlySharedStringsTable strings = new ReadOnlySharedStringsTable(pkg);
        XSSFReader.SheetIterator ite = (XSSFReader.SheetIterator) xssfReader.getSheetsData();
        
        if(sheetNames.length == 0){
            while(ite.hasNext()){
                InputStream sheetInputStream = ite.next();
                processSheet(styles, strings, sheetInputStream, null);
            }
        }else{
            while(ite.hasNext()){
                InputStream sheetInputStream = ite.next();
                for(String name : sheetNames){
                    if(sheetNames.equals(ite.getSheetName())){
                        processSheet(styles, strings, sheetInputStream, name);
                    }
                    
                }
            }
        }
    }
    
    private void processSheet(StylesTable styles, ReadOnlySharedStringsTable strings, InputStream sheetInputStream, String sheetName) throws IOException, SAXException, ParserConfigurationException{
        XMLReader sheetParser = SAXHelper.newXMLReader();
        sheetParser.setContentHandler(new XSSFSheetXMLHandler(styles, strings, new SimpleSheetContentsHandler(sheetName), false));
        sheetParser.parse(new InputSource(sheetInputStream));
    }
    
    /**It's a example class used to handle the sheet in excel file.*/
    private class SimpleSheetContentsHandler implements SheetContentsHandler{
        protected LinkedList<String> rows = new LinkedList<String>();
        
        private String sheetName;
        
        public SimpleSheetContentsHandler(String sheetName){
            this.sheetName = sheetName;
        }
        
        public SimpleSheetContencsHandler(){
            this(null);
        }
        
        public void startRow(int rowNum){
            // The action when you start getting rows
            rows.clear();
        }
        
        public void endRow(){
            // The action when you got all rows
            if(!rows.isEmpty()){
            	context.add(rows);
            }
        }
        
        public void cell(String cellReference, String formattedValue){
            // The action when you get a row
            rows.add(formattedValue);
        }
        
        public void headerFooter(String text, boolean isHeader, String tagName){
            //
        }
    }
    
    public LinkedList<LinkedList<String>> getContext(){
        return context;
    }
}


```
