 let boolVal = UserDefaults.standard.value(forKey: "isFromLogin") as? Bool
        
        if boolVal != nil
        {
            if boolVal! {
                UserDefaults.standard.set(false, forKey: "isFromLogin")
                UserDefaults.standard.synchronize()
                self.view.removeFromSuperview()
               
            }else{
                let SpleCeVC = storyboard?.instantiateViewController(withIdentifier: "SplashViewController") as! SplashViewController
                self.view.addSubview(SpleCeVC.view)
                self.view.bringSubview(toFront: SpleCeVC.view)
                self.addChildViewController(SpleCeVC)
               
            }
        }else{
            let SpleCeVC = storyboard?.instantiateViewController(withIdentifier: "SplashViewController") as! SplashViewController
            self.view.addSubview(SpleCeVC.view)
            self.view.bringSubview(toFront: SpleCeVC.view)
            self.addChildViewController(SpleCeVC)
           
            
        }
        
       self.btnLogin.CircleRoundsMainButton()
    }









===========================================
// statusBar View
extension UIApplication {
    var statusBarView: UIView? {
        return value(forKey: "statusBar") as? UIView
    }
}

///AppDelegate set
//StatesBar
        UIApplication.shared.statusBarView?.backgroundColor = COLOR_COMMON_DARK_ORNAGE
        
==========================================================================================================================        
Folder Name Color

import Foundation
import UIKit


let COLOR_COMMON_DARK_ORNAGE = UIColor(hexString: "#f67f27")
let COLOR_COMMON_LIGHT_ORNAGE = UIColor(hexString: "#fe903e")

let COLOR_COMMON_DARK_BLACK = UIColor(hexString: "#272828")
let COLOR_COMMON_LIGHT_BLACK = UIColor(hexString: "#595959")

let COLOR_TEXTFIELD_BG = UIColor(red: 246/255, green: 246/255, blue: 246/255, alpha: 1)
 
let COLOR_BUTTON_SELECTED_BG = COLOR_COMMON_LIGHT_ORNAGE
let COLOR_BUTTON_UNSELECTED_BG = UIColor(red: 246/255, green: 246/255, blue: 246/255, alpha: 1)

let COLOR_BUTTON_SELECTED_Text = UIColor.white
let COLOR_BUTTON_UNSELECTED_Text = COLOR_COMMON_LIGHT_BLACK

extension UIColor {
    convenience init(hexString: String) {
        var cString:String =  hexString.trimmingCharacters(in: .whitespacesAndNewlines).uppercased()
        
        if (cString.hasPrefix("#")) {
            cString = cString.substring(from: cString.characters.index(cString.startIndex, offsetBy: 1))
        }
        
        var red : CGFloat = 0.0
        var green : CGFloat = 0.0
        var blue : CGFloat = 0.0
        
        if ((cString.characters.count) != 6) {
            red = 0
            green = 0
            blue = 0
        } else {
            var rgbValue:UInt32 = 0
            Scanner(string: cString).scanHexInt32(&rgbValue)
            red = CGFloat((rgbValue & 0xFF0000) >> 16) / 255.0
            green = CGFloat((rgbValue & 0x00FF00) >> 8) / 255.0
            blue = CGFloat(rgbValue & 0x0000FF) / 255.0
        }
        self.init(red: red, green: green, blue: blue, alpha: 1.0)
    }
}


extension CALayer {
    
    func addBorder(_ edge: UIRectEdge, color: UIColor, thickness: CGFloat) {
        
        let border = CALayer();
        
        switch edge {
        case UIRectEdge.top:
            border.frame = CGRect(x: 0, y: 0, width: self.frame.width, height: thickness);
            break
        case UIRectEdge.bottom:
            border.frame = CGRect(x: 0, y: self.frame.height - thickness, width: self.frame.width, height: thickness)
            break
        case UIRectEdge.left:
            border.frame = CGRect(x: 0, y: 0, width: thickness, height: self.frame.height)
            break
        case UIRectEdge.right:
            border.frame = CGRect(x: self.frame.width - thickness, y: 0, width: thickness, height: self.frame.height)
            break
//        case UIRectEdge.All:
//            border.frame = self.bounds
//            break
        default:
            break
        }
        
        border.backgroundColor = color.cgColor;
        
        self.addSublayer(border)
    }
}
===========================================================================================================================

ButtonDesign.swift


import Foundation
import UIKit

@IBDesignable
class OptionsUIButton: UIButton {
    
    @IBInspectable var isButtonSelected: Bool = false {
        didSet {
            if isButtonSelected {
                self.layer.cornerRadius = 5.0
                self.backgroundColor = COLOR_BUTTON_SELECTED_BG
                self.setTitleColor(COLOR_BUTTON_SELECTED_Text, for: .normal)
                self.clipsToBounds = true
            }
            else {
                self.layer.cornerRadius = 5.0
                self.backgroundColor = COLOR_BUTTON_UNSELECTED_BG
                self.setTitleColor(COLOR_BUTTON_UNSELECTED_Text, for: .normal)
                self.clipsToBounds = true
            }
        }
    }
}


extension OptionsUIButton {
    
    override var isSelected: Bool {
        willSet {
        }
        didSet {
            if self.isSelected {
                self.isButtonSelected = true
            } else {
                self.isButtonSelected = false
            }
        }
    }
}


extension OptionsUIButton {
    
    func selectedSelfFrom(_ fromButtons : [OptionsUIButton]) {
        self.isSelected = true
        for btn in fromButtons {
            if self != btn {
                btn.isSelected = false
            }
        }
    }
}
===========================================================================================================================
UITextFiled and Picker


import Foundation
import UIKit

@IBDesignable
class DesignableUITextField: UITextField {
    /*
     required init(coder aDecoder: NSCoder) {
     super.init(coder: aDecoder)!
     initialSettings()
     }
     */
    
    var picker = UIPickerView()
    var pickerArrayData = [String]()
    var pickerDidSelect : ((Int) -> Void)?
    
    var datePicker = UIDatePicker()
    var strDateFormat = String()
    var datePickerDidSelect : ((Date) -> Void)?

    
    func initialSettings(){
        self.borderStyle = .none
        self.backgroundColor = COLOR_TEXTFIELD_BG
        self.layer.cornerRadius = 5.0
        self.layer.masksToBounds = true
         self.autocorrectionType = .no
    }
    
    @IBInspectable var setBGColor : Bool = true {
        didSet {
            if setBGColor {
                initialSettings()
            }
        }
    }
    
    // Provides left padding for images
    override func leftViewRect(forBounds bounds: CGRect) -> CGRect {
        var textRect = super.leftViewRect(forBounds: bounds)
        textRect.origin.x += leftPadding
        return textRect
    }
    
    override func rightViewRect(forBounds bounds: CGRect) -> CGRect {
        let textRect = super.rightViewRect(forBounds: bounds)

        return textRect
    }
    
    @IBInspectable var leftImage: UIImage? {
        didSet {
            updateView()
        }
    }
    
    
    @IBInspectable var leftPadding: CGFloat = 0 {
        didSet {
            updateView()
        }
    }
    
    @IBInspectable var rightImage: UIImage? {
        didSet {
            updateView()
        }
    }
    
    @IBInspectable var rightPadding: CGFloat = 0 {
        didSet {
            updateView()
        }
    }
    
    //    @IBInspectable var tintColor: UIColor = UIColor.lightGray {
    //        didSet {
    //            updateView()
    //        }
    //    }
    
    func updateView() {
        
        if let image = leftImage {
            leftViewMode = UITextFieldViewMode.always
            let imageView = UIImageView(image: image)
            imageView.image = image
            imageView.contentMode = .center
            //            imageView.frame = CGRect(x: 0, y: 0, width: imageView.frame.size.width + 10.0, height: self.frame.size.height)
            imageView.frame = CGRect(x: 0, y: 0, width: self.frame.size.height, height: self.frame.size.height)
            imageView.backgroundColor = UIColor.clear
            // Note: In order for your image to use the tint color, you have to select the image in the Assets.xcassets and change the "Render As" property to "Template Image".
            //            imageView.tintColor = color
            leftView = imageView
        }
        else if leftPadding == 0 {
            leftViewMode = UITextFieldViewMode.never
            leftView = nil
        }
        else if leftPadding != 0 {
            leftViewMode = UITextFieldViewMode.always
            //            let newLeftView = UIView(frame: CGRect(x: 0, y: 0, width: leftPadding, height: self.frame.size.height))
            let newLeftView = UIView(frame: CGRect(x: 0, y: 0, width: 0, height: 0))
            newLeftView.backgroundColor = UIColor.clear
            newLeftView.isUserInteractionEnabled = false
            leftView = newLeftView
        }
        
        
        if let image = rightImage {
            rightViewMode = UITextFieldViewMode.always
            let imageView = UIImageView(image: image)
            imageView.image = image
            imageView.contentMode = .center
            //            imageView.frame = CGRect(x: 0, y: 0, width: imageView.frame.size.width + 10.0 , height: self.frame.size.height)
            imageView.frame = CGRect(x: 0, y: 0, width: self.frame.size.height , height: self.frame.size.height)
            imageView.backgroundColor = UIColor.clear
            //            rightView = imageView
            
            let newRightView = UIView(frame: CGRect(x: 0, y: 0, width: rightPadding + imageView.frame.size.width, height: self.frame.size.height))
            imageView.frame = CGRect(x: newRightView.frame.size.width - imageView.frame.size.width, y: 0, width: self.frame.size.height , height: self.frame.size.height)
            newRightView.addSubview(imageView)
            newRightView.backgroundColor = UIColor.clear
            newRightView.isUserInteractionEnabled = false
            rightView = newRightView
        }
        else if rightPadding == 0 {
            rightViewMode = UITextFieldViewMode.never
            rightView = nil
        }
        else if rightPadding != 0 {
            rightViewMode = UITextFieldViewMode.always
            let newRightView = UIView(frame: CGRect(x: 0, y: 0, width: rightPadding, height: self.frame.size.height))
            //            let newRightView = UIView(frame: CGRect(x: 0, y: 0, width: 0, height: 0))
            newRightView.backgroundColor = UIColor.clear
            newRightView.isUserInteractionEnabled = false
            rightView = newRightView
        }
        
        // Placeholder text color
        //        attributedPlaceholder = NSAttributedString(string: placeholder != nil ?  placeholder! : "", attributes:[NSForegroundColorAttributeName: color])
        
    }
}



//NOTE:-
// BELOW is for Picker View
extension DesignableUITextField : UIPickerViewDataSource, UIPickerViewDelegate {
    
    func addPickerView(_ arrData : [String] ,  callback: ((Int) -> Void)?) {
        pickerArrayData = arrData
        pickerDidSelect = callback
        picker = UIPickerView()
        picker.dataSource = self
        picker.delegate = self
        picker.reloadAllComponents()
        self.inputView = picker
    }
    
    func numberOfComponents(in pickerView: UIPickerView) -> Int {
        return 1
    }
    
    func pickerView(_ pickerView: UIPickerView, numberOfRowsInComponent component: Int) -> Int {
        return pickerArrayData.count
    }
    
    func pickerView(_ pickerView: UIPickerView, titleForRow row: Int, forComponent component: Int) -> String? {
        return pickerArrayData[row]
    }
    
    func pickerView(_ pickerView: UIPickerView, didSelectRow row: Int, inComponent component: Int) {
        //        print("\n************* didSelectRow **********************************\n")
        self.text = pickerArrayData[row]
        if pickerDidSelect != nil {
            pickerDidSelect!(row)
        }
    }
}


extension DesignableUITextField {
    func addDatePicker(mode : UIDatePickerMode, dateFormat : String, callback: ((Date) -> Void)?) {
        datePickerDidSelect = callback
        strDateFormat = dateFormat
        datePicker = UIDatePicker()
        datePicker.datePickerMode = mode
        self.inputView = datePicker
        
        datePicker.addTarget(self, action: #selector(dateChanged(_:)), for: .valueChanged)
    }
    
    func dateChanged(_ sender: UIDatePicker){
        //For date formate
        let formatter = DateFormatter()
        formatter.dateFormat = strDateFormat
        self.text = formatter.string(from: datePicker.date)
        if datePickerDidSelect != nil {
            datePickerDidSelect!(datePicker.date)
        }
    }
}
============================================================================================================================
import Foundation
import UIKit

extension UIViewController {
    func addInputAccessoryForTextFields(textFields: [UITextField],
        previousNextable: Bool = false)
    {
        for (index, textField) in textFields.enumerated() {
            let toolbar: UIToolbar = UIToolbar()
            toolbar.sizeToFit()
            
            var items = [UIBarButtonItem]()
            if previousNextable {
                let previousButton = UIBarButtonItem(image: UIImage(named: "backwordArrow"), style: .plain, target: nil, action: nil)
                previousButton.tintColor = COLOR_COMMON_DARK_ORNAGE
                previousButton.width = 30
                if textField == textFields.first {
                    previousButton.isEnabled = false
                } else {
                    
                    previousButton.target = textFields[index - 1]
                    previousButton.action = #selector(UITextField.becomeFirstResponder)
                }
                
                let nextButton = UIBarButtonItem(image: UIImage(named: "forwordArrow"), style: .plain, target: nil, action: nil)
                nextButton.tintColor = COLOR_COMMON_DARK_ORNAGE
                nextButton.width = 30
                if textField == textFields.last {
                    nextButton.isEnabled = false
                } else {
                    
                    nextButton.target = textFields[index + 1]
                    nextButton.action = #selector(UITextField.becomeFirstResponder)
                }
                
                items.append(contentsOf: [previousButton, nextButton])
            }
            
            let spacer = UIBarButtonItem(barButtonSystemItem: .flexibleSpace, target: nil, action: nil)
            let doneButton = UIBarButtonItem(barButtonSystemItem: .done, target: view, action: #selector(UIView.endEditing))
            items.append(contentsOf: [spacer, doneButton])
            doneButton.tintColor = COLOR_COMMON_DARK_ORNAGE
            toolbar.setItems(items, animated: false)
            textField.inputAccessoryView = toolbar
        }
    }
}
============================================================================================================================

//  Pref.swift

import Foundation


let kISLogInUser = "ISLogInUser"
let krmId = "rmId"
let kCustomerId = "kCustomerId"
let KprofileName = "profileName"

class Pref {
    class func setNSObject(_ obj:NSObject? , forKey:String) {
        let userDefaults = UserDefaults.standard
        if obj == nil {
            userDefaults.set(nil, forKey: forKey)
            userDefaults.synchronize()
        }
        else {
            let data = NSKeyedArchiver.archivedData(withRootObject: obj!)
            userDefaults.set(data, forKey: forKey)
            userDefaults.synchronize()
        }
    }
    
    class func setAnyObject(_ obj:Any? , forKey:String) {
        let userDefaults = UserDefaults.standard
        
        if obj == nil {
            userDefaults.set(obj, forKey: forKey)
            userDefaults.synchronize()
            return
        }
        else if obj is NSURL {
            userDefaults.set(obj as? URL, forKey: forKey)
        }
        else {
            userDefaults.set(obj, forKey: forKey)
        }
        userDefaults.synchronize()
    }
    
    class func getObjectForKey(_ theKey:String) -> AnyObject? {
        let userDefaults = UserDefaults.standard
        let resultObject = userDefaults.object(forKey: theKey)
        if resultObject != nil && resultObject is Data {
            return NSKeyedUnarchiver.unarchiveObject(with: resultObject as! Data) as AnyObject?
        }
        return UserDefaults.standard.object(forKey: theKey) as AnyObject?
    }
}

============================================================================================================================

Scroling Animation

class hederVC: UIViewController {

    @IBOutlet weak var imgView: UIImageView!
    var itemIndex: Int = 0
    
    var imageName: String = "" {
        didSet {
            if let imageView = imgView {
                imageView.image = UIImage(named: imageName)
            }
        }
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.setView()
    }
    
    
    
    func setView() {
        self.imgView.layer.cornerRadius = 5.5
        self.imgView.image = UIImage(named: imageName)
    }


}
----------------------------------
VC Animation



@IBOutlet weak var viewscrolling: ViewPager!
    var arrOfferImage = ["Image-10",
                         "Image-11",
                         "Image-12",
                         "Image-13",
                         "Image-14"]
    override func viewDidLoad() {
        super.viewDidLoad()
        self.setView()
    }
    //MARK: - Other methods
        func setView() {
            
            self.subviewOfferImageSetUp()
        }
    
    func numberOfItems(viewPager: ViewPager) -> Int {
        return arrOfferImage.count
    }
    
    func viewAtIndex(viewPager: ViewPager, index: Int, view: UIView?) -> UIView {
        let objHeaderViewPagerVC = hederVC(nibName: "hederVC", bundle: nil)
        objHeaderViewPagerVC.imageName = arrOfferImage[index]
        return objHeaderViewPagerVC.view
    }
    ==================================================
    
    import UIKit

@objc public protocol  ViewPagerDataSource {
    func numberOfItems(viewPager:ViewPager) -> Int
    func viewAtIndex(viewPager:ViewPager, index:Int, view:UIView?) -> UIView
    @objc optional func didSelectedItem(index:Int)
//    optional func didSelectedLike(index:Int)
//    optional func didSelectedAddToCollection(index:Int)
    
}

public class ViewPager: UIView {
    
    var pageControl:UIPageControl = UIPageControl()
    var scrollView:UIScrollView = UIScrollView()
    var currentPosition:Int = 0
    
    var dataSource:ViewPagerDataSource? = nil {
        didSet {
            reloadData()
        }
    }
    
    var numberOfItems:Int = 0
    var itemViews:Dictionary<Int, UIView> = [:]
    
    required  public init?(coder aDecoder: NSCoder) {
        super.init(coder: aDecoder)
        setupView()
    }
    
    override init(frame: CGRect) {
        super.init(frame: frame)
        setupView()
    }
    
    func setupView() {
        self.addSubview(scrollView)
        self.addSubview(pageControl)
        setupScroolView();
        setupPageControl();
        
        
        reloadData()
    }
    
    func setupScroolView() {
        scrollView.isPagingEnabled = true;
        scrollView.alwaysBounceHorizontal = false
        scrollView.bounces = false
        scrollView.backgroundColor = UIColor.clear //AppColor.AppTheme_Primary
        scrollView.showsHorizontalScrollIndicator = false
        scrollView.showsVerticalScrollIndicator = false
        scrollView.delegate = self;
        scrollView.tag = 10
        let topContraints = NSLayoutConstraint(item: scrollView, attribute:
            .top, relatedBy: .equal, toItem: self,
                  attribute: NSLayoutAttribute.top, multiplier: 1.0,
                  constant: 0)
        
        let bottomContraints = NSLayoutConstraint(item: scrollView, attribute:
            .bottom, relatedBy: .equal, toItem: self,
                     attribute: NSLayoutAttribute.bottom, multiplier: 1.0,
                     constant: 0)
        
        let leftContraints = NSLayoutConstraint(item: scrollView, attribute:
            .leadingMargin, relatedBy: .equal, toItem: self,
                            attribute: .leadingMargin, multiplier: 1.0,
                            constant: 0)
        
        let rightContraints = NSLayoutConstraint(item: scrollView, attribute:
            .trailingMargin, relatedBy: .equal, toItem: self,
                             attribute: .trailingMargin, multiplier: 1.0,
                             constant: 0)
        
        
        scrollView.translatesAutoresizingMaskIntoConstraints = false
        NSLayoutConstraint.activate([topContraints,rightContraints,leftContraints,bottomContraints])
    }
    
    func setupPageControl() {
        
        self.pageControl.numberOfPages = numberOfItems
        self.pageControl.currentPage = 0
        
        self.pageControl.pageIndicatorTintColor = UIColor.lightGray
        self.pageControl.currentPageIndicatorTintColor = UIColor.red
        self.pageControl.isUserInteractionEnabled = false
        
        
        let heightContraints = NSLayoutConstraint(item: pageControl, attribute:
            .height, relatedBy: .equal, toItem: nil,
                     attribute: NSLayoutAttribute.notAnAttribute, multiplier: 1.0,
                     constant: 25)
        
        let bottomContraints = NSLayoutConstraint(item: pageControl, attribute:
            .bottom, relatedBy: .equal, toItem: self,
                     attribute: NSLayoutAttribute.bottom, multiplier: 1.0,
                     constant: 0)
        
        let leftContraints = NSLayoutConstraint(item: pageControl, attribute:
            .leadingMargin, relatedBy: .equal, toItem: self,
                            attribute: .leadingMargin, multiplier: 1.0,
                            constant: 0)
        
        let rightContraints = NSLayoutConstraint(item: pageControl, attribute:
            .trailingMargin, relatedBy: .equal, toItem: self,
                             attribute: .trailingMargin, multiplier: 1.0,
                             constant: 0)
        
        
        pageControl.translatesAutoresizingMaskIntoConstraints = false
        NSLayoutConstraint.activate([heightContraints,rightContraints,leftContraints,bottomContraints])
    }
    
    
    func reloadData() {
        if(dataSource != nil){
            numberOfItems = (dataSource?.numberOfItems(viewPager: self))!
        }
        self.pageControl.numberOfPages = 2
        
        itemViews.removeAll()
        for view in self.scrollView.subviews {
            view.removeFromSuperview()
        }
        
        DispatchQueue.main.async {
            () -> Void in
         
            self.scrollView.contentSize = CGSize.init(width: self.scrollView.frame.width *  CGFloat(self.numberOfItems), height: self.scrollView.frame.height)
            self.reloadViews(index: 0)
        }
    }
    
    func loadViewAtIndex(index:Int){
        let view:UIView?
        if(dataSource != nil){
            view =  (dataSource?.viewAtIndex(viewPager: self, index: index, view: itemViews[index]))!
        }else{
            view = UIView()
        }
        
        setFrameForView(view: view!, index: index);
        
        
        
        if(itemViews[index] == nil){
            itemViews[index] = view
            let tap = UITapGestureRecognizer(target: self, action:  #selector(self.handleTapSubView))
            tap.numberOfTapsRequired = 1
            
//            if(view?.subviews.count > 3)
//            {
//                let tapLike = UITapGestureRecognizer(target: self, action:  #selector(self.like))
//                tap.numberOfTapsRequired = 1
//
//                itemViews[index]!.subviews[3].addGestureRecognizer(tapLike)
//                
//                let tapaddCollection = UITapGestureRecognizer(target: self, action:  #selector(self.addCollection))
//                tap.numberOfTapsRequired = 1
//                
//                itemViews[index]!.subviews[1].addGestureRecognizer(tapaddCollection)
//            }
            itemViews[index]!.addGestureRecognizer(tap)
            scrollView.addSubview(itemViews[index]!)
        }else{
            itemViews[index] = view
        }
        
    }
  
//    func like() {
//    
//        if(dataSource?.didSelectedLike != nil){
//            dataSource?.didSelectedLike!(currentPosition)
//        }
//    }
//    func addCollection() {
//        
//        if(dataSource?.didSelectedAddToCollection != nil){
//            dataSource?.didSelectedAddToCollection!(currentPosition)
//        }
//    }
    
    @objc func handleTapSubView() {
        if(dataSource?.didSelectedItem != nil){
            dataSource?.didSelectedItem!(index: currentPosition)
        }
    }
    
    
    func reloadViews(index:Int){
        
        for i in (index-1)...(index+1) {
            if(i>=0 && i<numberOfItems){
                loadViewAtIndex(index: i);
            }
        }
        // print(scrollView.subviews.count)
    }
    
    func setFrameForView(view:UIView,index:Int){
        view.frame = CGRect(x: self.scrollView.frame.width*CGFloat(index), y: 0, width: self.scrollView.frame.width, height: self.scrollView.frame.height);
    }
}

extension ViewPager:UIScrollViewDelegate{
    
    public func scrollViewDidEndDecelerating(_ scrollView: UIScrollView) {
        
        let pageNumber = round(scrollView.contentOffset.x / scrollView.frame.size.width)
        
        if pageNumber.isNaN == false {
            pageControl.currentPage = Int(pageNumber)
            currentPosition = pageControl.currentPage
            reloadViews(index: Int(pageNumber));
        }
        
        
    }
    
    
}

extension ViewPager{
    
    
    func animationNext(){
//        NSTimer.scheduledTimerWithTimeInterval(2, target: self, selector: #selector(ViewPager.moveToNextPage), userInfo: nil, repeats: true)
    }
    func moveToNextPage (){
        if(currentPosition <= numberOfItems && currentPosition > 0) {
            scrollToPage(index: currentPosition)
            currentPosition = currentPosition + 1
            if currentPosition > numberOfItems {
                currentPosition = 1
            }
        }
    }
    
    func scrollToPage(index:Int) {
        if(index <= numberOfItems && index > 0) {
            let zIndex = index - 1
            let iframe = CGRect(x: self.scrollView.frame.width*CGFloat(zIndex), y: 0, width: self.scrollView.frame.width, height: self.scrollView.frame.height);
            scrollView.setContentOffset(iframe.origin, animated: true)
            pageControl.currentPage = zIndex
            reloadViews(index: zIndex)
            currentPosition = index
        }
    }
    
}
        
        func subviewOfferImageSetUp() {
            self.viewscrolling.dataSource = self
            DispatchQueue.main.async {
                self.ImagePageViewSetup()
            }
        }
        
        func ImagePageViewSetup() {
            self.viewscrolling.scrollViewDidEndDecelerating(self.viewscrolling.scrollView)
        }

============================================================================================================================



