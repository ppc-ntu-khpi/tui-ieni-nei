# UI Lab 1
Result:

![image](https://github.com/ppc-ntu-khpi/tui-ieni-nei/assets/113203792/d615fc0d-e7ec-44e3-908f-30ad3e101ac3)

(read form test.dat file)

![image](https://github.com/ppc-ntu-khpi/tui-ieni-nei/assets/113203792/1a674500-982d-4a02-bb88-97914d26f297)

```TUIdemo.java:```
```java
package com.mybank.tui;

import jexer.TAction;
import jexer.TApplication;
import jexer.TField;
import jexer.TText;
import jexer.TWindow;
import jexer.event.TMenuEvent;
import jexer.menu.TMenu;

import java.io.BufferedReader;
import java.io.FileReader;

/**
 *
 * @author Alexander 'Taurus' Babich
 */
public class TUIdemo extends TApplication {

    private static final int ABOUT_APP = 2000;
    private static final int CUST_INFO = 2010;

    public static void main(String[] args) throws Exception {
        TUIdemo tdemo = new TUIdemo();
        (new Thread(tdemo)).start();
    }

    public TUIdemo() throws Exception {
        super(BackendType.SWING);

        addToolMenu();
        //custom 'File' menu
        TMenu fileMenu = addMenu("&File");
        fileMenu.addItem(CUST_INFO, "&Customer Info");
        fileMenu.addDefaultItem(TMenu.MID_SHELL);
        fileMenu.addSeparator();
        fileMenu.addDefaultItem(TMenu.MID_EXIT);
        //end of 'File' menu

        addWindowMenu();

        //custom 'Help' menu
        TMenu helpMenu = addMenu("&Help");
        helpMenu.addItem(ABOUT_APP, "&About...");
        //end of 'Help' menu

        setFocusFollowsMouse(true);
        //Customer window
        ShowCustomerDetails();
    }

    @Override
    protected boolean onMenu(TMenuEvent menu) {
        if (menu.getId() == ABOUT_APP) {
            messageBox("About", "\t\t\t\t\t   Just a simple Jexer demo.\n\nCopyright \u00A9 2019 Alexander \'Taurus\' Babich").show();
            return true;
        }
        if (menu.getId() == CUST_INFO) {
            ShowCustomerDetails();
            return true;
        }
        return super.onMenu(menu);
    }

    private void ShowCustomerDetails() {
        TWindow custWin = addWindow("Customer Window", 2, 1, 40, 10, TWindow.NOZOOMBOX);
        custWin.newStatusBar("Enter valid customer number and press Show...");

        custWin.addLabel("Enter customer number: ", 2, 2);
        TField custNo = custWin.addField(24, 2, 3, false);
        TText details = custWin.addText("Owner Name: \nAccount Type: \nAccount Balance: ", 2, 4, 38, 8);
        custWin.addButton("&Show", 28, 2, new TAction() {
            @Override
            public void DO() {
                try {
                    int custNum = Integer.parseInt(custNo.getText());
                    
                    BufferedReader reader = new BufferedReader(new FileReader("test.dat"));
                    String line;
                    boolean found = false;
                    while ((line = reader.readLine()) != null) {
                        if (line.isEmpty()) continue; // Skip empty lines
                        
                        int customerNumber = Integer.parseInt(line);
                        if (customerNumber == custNum) {
                            found = true;
                            String ownerName = reader.readLine();
                            String accountType = reader.readLine().split("\\s+")[0];
                            String accountBalance = reader.readLine().split("\\s+")[1];
                            
                            details.setText("Owner Name: " + ownerName + "\nAccount Type: '" + accountType + "'\nAccount Balance: $" + accountBalance);
                            break;
                        } else {
                            reader.readLine();
                            reader.readLine();
                            reader.readLine();
                        }
                    }
                    reader.close();

                    if (!found) {
                        messageBox("Error", "Customer not found!").show();
                    }
                } catch (Exception e) {
                    messageBox("Error", "You must provide a valid customer number!").show();
                }
            }
        });
    }
}

```
