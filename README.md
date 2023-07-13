# KIOSK

![image](https://github.com/Poki742/KIOSK/assets/126844692/6edd233c-9583-4766-8e23-ce17512adb53)<br>


## AdminloginController java
``` java
package application;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import javafx.event.ActionEvent;
import javafx.fxml.FXML;
import javafx.fxml.FXMLLoader;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.scene.control.Alert;
import javafx.scene.control.Button;
import javafx.scene.control.PasswordField;
import javafx.scene.control.TextField;
import javafx.scene.control.Alert.AlertType;
import javafx.stage.Stage;

public class AdminloginController {

	@FXML Button LoginButtion, ClearButton, CloseButton;
	@FXML TextField IdTextField;
	@FXML PasswordField PwPasswordField;
	
	
	@FXML
	private void LoginButtonAction(ActionEvent event) {
		
		if(IdTextField.getText().isEmpty() || PwPasswordField.getText().isEmpty()){
			Alert alert = new Alert(AlertType.WARNING);
			alert.setTitle("바이러스 감지!!!!");
			alert.setHeaderText("누구야 너");
			alert.setContentText("제대로 입력해.");
			alert.show();
		}else {
			OracleDBconnect conn = new OracleDBconnect();
			Connection conn2 = conn.getconn();
			
			String sql = "select adminid, adminpw"
					+ "  from admin_accounts"
					+ "  where adminid = ?"
					+ "  and adminpw = ?";
			try {
				PreparedStatement ps = conn2.prepareStatement(sql);
				
				ps.setString(1, IdTextField.getText());
				ps.setString(2, PwPasswordField.getText());
				
				ResultSet rs = ps.executeQuery();
				
				if(rs.next()) {
					System.out.println("로그인 성공");
					
					try {
						Parent root = FXMLLoader.load(getClass().getResource("admindb.fxml"));
						Scene scene = new Scene(root);
						Stage stage = new Stage();
						stage.setScene(scene);
						stage.setTitle("관리자 DB 조회");
						stage.show();
					} catch(Exception e) {
						e.printStackTrace();
					}
					
					Stage stage = (Stage)CloseButton.getScene() .getWindow();
					stage.close();
					
					//CloseButtonAction(event);
					
				}else {
					Alert alert = new Alert(AlertType.WARNING);
					alert.setTitle("Are You Kidding Me?!");
					alert.setHeaderText("뭐냐?");
					alert.setContentText("틀렸잖아 다시적어.");
					alert.show();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}	
		}
	}
	
	@FXML
	private void ClearButttonAction(ActionEvent event) {
		
		IdTextField.setText("");
		PwPasswordField.setText("");
	}
	
	
	@FXML
	private void CloseButtonAction(ActionEvent event) {
		
		Stage stage = (Stage)CloseButton.getScene() .getWindow();
		stage.close();
	}
	
	
	
}
```
위의 코드는 JavaFX 적용에서 관리자 로그인 화면을 담당하는 컨트롤러 클래스입니다.<br>
이 클래스는 FXML 파일과 연결되어 UI 요소와 사용자가 입력을 처리하고 데이터베이스 연결을 수행하며<br>
다른 화면으로 이동을 관리합니다.<br>
## AdmindbController java
``` java

package application;

import java.net.URL;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ResourceBundle;

import javafx.collections.FXCollections;
import javafx.collections.ObservableList;
import javafx.event.ActionEvent;
import javafx.fxml.FXML;
import javafx.fxml.Initializable;
import javafx.scene.control.Button;
import javafx.scene.control.DatePicker;
import javafx.scene.control.TableColumn;
import javafx.scene.control.TableView;
import javafx.scene.control.TextArea;
import javafx.scene.control.cell.PropertyValueFactory;

public class AdmindbController implements Initializable {
	
	@FXML private Button searchButton, datasearchButton;
	@FXML private DatePicker dataDatePicker;
	@FXML private TextArea resultTextArea;
	@FXML private TableView<Orderlist> orderlistTableView;
	//<S> 각컬럼과 데이터형식이 일치하는 자료구조를 가진 클래스 파일명
		@FXML TableColumn<Orderlist, String> idxTableColumn;
		@FXML TableColumn<Orderlist, String> dateTableColumn;
		@FXML TableColumn<Orderlist, String> count1TableColumn;
		@FXML TableColumn<Orderlist, String> count2TableColumn;
		@FXML TableColumn<Orderlist, String> count3TableColumn;
		@FXML TableColumn<Orderlist, String> sumTableColumn;
		//TableColumn<s, t>
		//t는 s파일에서 선언한 변수의 데이터형
		
		@Override
		public void initialize(URL arg0, ResourceBundle arg1) {
			
			System.out.println("주문리스트 창이 열리고 초기화를 하려고 함");
			
			idxTableColumn.setCellValueFactory(new PropertyValueFactory<>("idx"));
			dateTableColumn.setCellValueFactory(new PropertyValueFactory<>("date"));
			count1TableColumn.setCellValueFactory(new PropertyValueFactory<>("count1"));
			count2TableColumn.setCellValueFactory(new PropertyValueFactory<>("count2"));
			count3TableColumn.setCellValueFactory(new PropertyValueFactory<>("count3"));
			sumTableColumn.setCellValueFactory(new PropertyValueFactory<>("sum")); 
		}
		
		@FXML
		private void searchButtonAction(ActionEvent event) {
			//디비 접속
			//sql를 이용해서 데이터 검색하기
			//쿼리 실행
			OracleDBconnect conn = new OracleDBconnect();
			Connection conn2 = conn.getconn();
			
			//주문리스트 테이블에 있는 자료 검색하기(정렬기준 idx)
			String sql = " select idx, order_time, count1, count2, count3, sum"
							+" from orderlist_accounts"
							+" order by idx";
			
			try {
				PreparedStatement ps = conn2.prepareStatement(sql);
				ResultSet rs = ps.executeQuery();
				
				
				 ObservableList<Orderlist> datalist = FXCollections.observableArrayList();
				
				while(rs.next()) {
					
					datalist.add(
							new Orderlist(
									rs.getString(1),
									rs.getString(2),
									rs.getString(3),
									rs.getString(4),
									rs.getString(5),
									rs.getString(6)
									)
							);
				}
				
				orderlistTableView.setItems(datalist);
				
			} catch (SQLException e) {
				e.printStackTrace();
			}
			
		}
		
		@FXML
		private void datasearchButtonAction(ActionEvent event) {
			
			
		}
		
		
		
		
}

```
