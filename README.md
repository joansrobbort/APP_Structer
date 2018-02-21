//
//  Setting_ViewController.swift
//  ClassPass
//
//  Created by vivek versatile on 11/02/17.
//  Copyright © 2017 Versatile Techno MAC-2. All rights reserved.
//

import UIKit
import MessageUI
class Setting_ViewController: UIViewController,ServerCallDelegate,MFMailComposeViewControllerDelegate,UITableViewDelegate,UITableViewDataSource{
    
    
    @IBOutlet weak var tblViewDiscription: UITableView!
    @IBOutlet weak var txtTreamCondition: UITextView!
    var sectingTag :Int = 100000
    var headerTitles = [String]()
    var ArrySetting = [SettingBean]()
    
    @IBOutlet weak var webViewPrivacyPolicy: UIWebView!
    @IBOutlet weak var CoomunitionView: UIView!
    @IBOutlet weak var TermContionView: UIView!
    @IBOutlet weak var PrivacyPolicyView: UIView!
    @IBOutlet weak var HelpCenterView: UIView!
    @IBOutlet weak var btnLogout: UIButton!
    @IBOutlet weak var btnMenubar: UIButton!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.CoomunitionView.isHidden = true
        self.TermContionView.isHidden = true
        self.HelpCenterView.isHidden = true
        self.PrivacyPolicyView.isHidden = true
        self.btnLogout.CircleRoundsuiButton()
        self.webViewPrivacyPolicy.backgroundColor = UIColor.white
        btnMenubar.addTarget(self.revealViewController(), action: #selector(self.revealViewController().revealToggle(_:)), for: .touchUpInside)
        self.revealViewController().shouldUseFrontViewOverlay = true
        self.view.addGestureRecognizer(self.revealViewController().tapGestureRecognizer())
        if !MFMailComposeViewController.canSendMail() {
            print("Mail services are not available")
            return
        }
        webViewPrivacyPolicy.backgroundColor = UIColor.white
    }
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        tblViewDiscription.delegate = self
        tblViewDiscription.dataSource = self
        tblViewDiscription.estimatedRowHeight = 100.0
        tblViewDiscription.rowHeight = UITableViewAutomaticDimension
        
    }

    //MARK :- EMail
    func configuredMailComposeViewController(){
        
        if !MFMailComposeViewController.canSendMail() {
            
            let alert = UIAlertController(title: "Alert!", message: "Please Add your mailId In Your Device", preferredStyle: UIAlertControllerStyle.alert)
            
            alert.addAction(UIAlertAction(title: "OK", style: .default, handler: { (action : UIAlertAction) in
                _ = self.navigationController?.popViewController(animated:true)
            }))
            
            self.present(alert, animated: true, completion: nil)
            return
        } else {
            let composeVC = MFMailComposeViewController()
            composeVC.mailComposeDelegate = self
            composeVC.setToRecipients(["info@o2class.com"])
            composeVC.setSubject("")
            composeVC.setMessageBody("", isHTML:false)
            self.present(composeVC, animated: true, completion: nil)
        }
        
        
    }
    func mailComposeController(_ controller: MFMailComposeViewController, didFinishWith result: MFMailComposeResult, error: Error?) {
        controller.dismiss(animated: true, completion: nil)
    }
    //MARK :- TableView Method
    func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
        return UITableViewAutomaticDimension
    }
    
    func numberOfSections(in tableView: UITableView) -> Int {
        return ArrySetting.count
    }
    func tableView(_ tableView: UITableView, heightForHeaderInSection section: Int) -> CGFloat {
        return 40
        
    }
    func tableView(_ tableView: UITableView, viewForHeaderInSection section: Int) -> UIView?
    {
        let viewHeaderFrame = CGRect(x: 0, y: 0, width: tableView.bounds.size.width, height: 40)
        let headerView = UIView(frame: viewHeaderFrame)
        headerView.backgroundColor =  UIColor(red: 249.0/255.0, green: 107.0/255.0, blue: 49.0/255.0, alpha: 1.0)
        
        let Button = UIButton(frame: CGRect(x: 0, y: 0, width: tableView.bounds.size.width, height: 30))
        let Bean = ArrySetting [section]
        Button.setTitle(Bean.headerName, for: .normal)
        Button.addTarget(self, action: #selector(TapToHide(_:)), for: .touchUpInside)
        Button.tag = section
        
        let frameSeprator = CGRect(x: 0, y: viewHeaderFrame.size.height - 1, width: viewHeaderFrame.size.width, height: 1)
        let lblSaprator = UILabel(frame: frameSeprator)
        lblSaprator.backgroundColor = UIColor.white
        
        headerView.addSubview(lblSaprator)
        headerView.addSubview(Button)
        headerView.bringSubview(toFront: lblSaprator)
        
        
        
        return headerView
    }
    func TapToHide(_ sender:UIButton){
        
        if sectingTag == sender.tag {
            sectingTag =  1000000000
        }else{
            sectingTag = sender.tag
//            print(sectingTag)
//            print(sender.tag)
        }
        tblViewDiscription.reloadData()
        
    }
    func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
        
        if section < ArrySetting.count {
            let Bean = ArrySetting[section]
            return Bean.headerName
        }
        
        return nil
    }
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        
        if sectingTag == section
            
        {
           // print(section)
            return 1
            
        }
        return 0
    }
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell: helpCell = tableView.dequeueReusableCell(withIdentifier: "Cell")as! helpCell
        var Bean = SettingBean()
        Bean = ArrySetting[indexPath.section]
        cell.lblDiscription.text = Bean.Description
        return cell
    }
    //MARK :- UIButton action
    @IBAction func TapToPrivacyPolicy(_ sender: Any) {
        self.view.bringSubview(toFront: PrivacyPolicyView)
        self.PrivacyPolicyView.isHidden = false
        self.HelpCenterView.isHidden = true
        self.TermContionView.isHidden = true
        self.CoomunitionView.isHidden = true
        self.PrivacyPolicy()
        
        
    }
    @IBAction func TapToTermToCondition(_ sender: Any) {
        
        self.view.bringSubview(toFront: TermContionView)
        self.TermContionView.isHidden = false
        self.PrivacyPolicyView.isHidden = true
        self.HelpCenterView.isHidden = false
        self.CoomunitionView.isHidden = true
        self.TrumCondition()
    }
    @IBAction func TapToHelpCenter(_ sender: Any) {
        self.view.bringSubview(toFront: HelpCenterView)
        self.HelpCenterView.isHidden = false
        self.PrivacyPolicyView.isHidden = true
        self.TermContionView.isHidden = true
        self.CoomunitionView.isHidden = true
        self.HelpCenterURl()
        
    }
    @IBAction func TapToEmail(_ sender: Any) {
        self.configuredMailComposeViewController()
    }
    @IBAction func TapToContect(_ sender: Any) {
        
        //        if let url = NSURL(string: "tel://\(96597433049)"), UIApplication.shared.canOpenURL(url as URL) {
        //            UIApplication.shared.openURL(url as URL)
        //        }
        //          else {
        //            let alert = UIAlertController(title: "Alert!", message: "Your device doesn’t support calling.", preferredStyle: UIAlertControllerStyle.alert)
        //                    alert.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default, handler: nil))
        //                    self.present(alert, animated: true, completion: nil)
        //                }
    
    }
    @IBAction func TapToCommunity(_ sender: Any) {
        self.view.bringSubview(toFront: CoomunitionView)
        self.CoomunitionView.isHidden = false
        self.PrivacyPolicyView.isHidden = true
        self.TermContionView.isHidden = true
        
        
    }
    @IBAction func BackToHelpCenter(_ sender: Any) {
        
        self.HelpCenterView.isHidden = true
        self.PrivacyPolicyView.isHidden = true
        self.TermContionView.isHidden = true
        self.CoomunitionView.isHidden = true
        
        
    }
    @IBAction func TapToLogOut(_ sender: UIButton) {
        self.SavePlan()
    }
    //MArk:- GETPRIVACY & Policy
    func HelpCenterURl(){
        SVProgressHUD.show(withStatus: "")
        ServerCall.sharedInstance.requestWithURL(.http_GET, urlString: URL_HELPCENTER, delegate: self, name: .helpCenter)
    }
    
    func PrivacyPolicy(){
        let req = URLRequest(url: URL(string: "http://o2class.com/privacy-policy.php")!)
        webViewPrivacyPolicy.loadRequest(req)
    }
    
    func SavePlan(){
        SVProgressHUD.show()
        if let UID = Pref.getObjectForKey(kUserBean) as? UserBean{
            let PlanId = Pref.getObjectForKey(KPurchaase_planID)
            let Param = ["user_id" : "\(UID.userID)",
                "plan_id" :PlanId]
            
            print(Param)
            ServerCall.sharedInstance.requestWithUrlAndParameters(.http_GET, urlString:URL_SAVEPLAN , parameters: Param as [String : AnyObject], delegate: self, name: .GetSavePlan)
        }
    }
    
    func TrumCondition(){
        SVProgressHUD.show(withStatus: "")
        let Param = ["page_id" : "\(1)"]
        print(Param)
        ServerCall.sharedInstance.requestWithUrlAndParameters(.http_POST, urlString: URL_PRIVACYPOLICY, parameters: Param as [String : AnyObject], delegate: self, name: .PrivacyPolicy)
    }
    
    func ServerCallSuccess(_ resposeObject: AnyObject, name: ServerCallName) {
        print("\n\n\n SUCCESS Privacy&Policy ........... \n\(resposeObject)")
        SVProgressHUD.dismiss()
        
        
        ResponceSetting(resposeObject)
        
            
         if name == .GetSavePlan {
            print("\n\n\n AvalableClass Sucess ........... \n \(resposeObject)")
            
            let DicData = resposeObject as! [String : AnyObject]
            if let States = DicData["status"]as? String{
                
                if States == "0"{
                    
                    let alert = UIAlertController(title: "", message: resposeObject["message"] as? String , preferredStyle: UIAlertControllerStyle.alert)
                    alert.addAction(UIAlertAction(title: "OK", style: .default, handler: { (action : UIAlertAction) in
                    }))
                    
                    self.present(alert, animated: true, completion: nil)
                    
                }
                else if States == "1"{
                    let TheLoginVc = self.storyboard?.instantiateViewController(withIdentifier: "SingInVC") as! UINavigationController
                    
                    for key in UserDefaults.standard.dictionaryRepresentation().keys{
                        if key != kDeviceToken
                        {
                            UserDefaults.standard.removeObject(forKey: key.description)
                        }
                        
                    }
                    print(Pref.getObjectForKey(kDeviceToken) ?? "popat")
                    let appDelegate = UIApplication.shared.delegate as! AppDelegate
                    appDelegate.window?.rootViewController = TheLoginVc
                    appDelegate.window?.makeKeyAndVisible()
                    
                    FBHelper.logout()
                    GIDSignIn.sharedInstance().signOut()
                    Pref.setObject(false as NSObject?, forKey: kIsLoggedIn)
                }
                else if States == "2"{
                    
                }
            }
        }
    }
    
    func ServerCallFailed(_ errorObject: String, name: ServerCallName) {
        SVProgressHUD.dismiss()
        print("\n\n\n Privacy Faile......... \n\(errorObject)")
        if name == .GetSavePlan{
            print("\n\n\n AvalableClass Sucess ........... \n \(errorObject)")
        }
    }
    
    
    func ResponceSetting(_ objResponse : AnyObject){
        ArrySetting.removeAll()
        if let DataDic = objResponse as? [String : Any],
            let DicData = DataDic["response_data"] as? [[String : Any]] {
            for  DicResult in DicData {
                let Bean = SettingBean()
                
                Bean.Description = TO_STRING(DicResult["txt_description"] ?? "")
                Bean.headerName = TO_STRING(DicResult["var_question"] ?? "")
                Bean.Id = TO_INT(DicResult["int_id"] ?? 0)
                print(Bean.Description)
                
                self.txtTreamCondition.text = Bean.Description
                
                ArrySetting.append(Bean)
            }
            self.tblViewDiscription.reloadData()
        }
        
    }
   
}
