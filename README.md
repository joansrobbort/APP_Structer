
enum SelectedScreen {
    case Spending
    case Transcation
    case Category
    case Account
}

protocol MultiSelectDelegate {
    func didSelectDelete(_ currentScreen : SelectedScreen)
    func didSelectEdit(_ currentScreen : SelectedScreen)
    func didSelectDone(_ currentScreen : SelectedScreen)
}

protocol TimePeriodDelegate {
    func didSelectTimePeriod(fromDate from_date : Date, to_date : Date)
}


class BaseViewController: UIViewController,UIActionSheetDelegate {
    
    //MARK:- ViewController Outlet
    @IBOutlet weak var lblweekly: UILabel!
    @IBOutlet weak var btnTimePeriod: UIButton!
    @IBOutlet weak var btnPrevious: UIButton!
    @IBOutlet weak var btnNext: UIButton!
    @IBOutlet weak var btnEdit: UIButton!
    @IBOutlet weak var lblTimePeriod: UILabel!
    @IBOutlet weak var lblselectedindex: UILabel!
    @IBOutlet weak var btnAccount: UIButton!
    @IBOutlet weak var btnMenu: UIButton!
    @IBOutlet weak var ViewselectedIndex: UIView!
    
    @IBOutlet weak var segmentControlMain: UISegmentedControl!
    var HomeVC = SpendingVC()
    @IBOutlet weak var containerSpending : UIView!
    @IBOutlet weak var containerTranscation: UIView!
    @IBOutlet weak var containerCategory: UIView!
    @IBOutlet weak var containerAccount: UIView!
    
    var currentScreen : SelectedScreen = SelectedScreen.Spending
    var SelectedViewDetaile = Bool()
    var selectedCatType : CategoryType!
    var selectedCatID : Int = 0
    
    var delegateMultiSelect : MultiSelectDelegate?
    var timePeriodDelegate : TimePeriodDelegate?
    
    private var Currentweek = Int()
    private var Currentmoth = Int()
    private var CurrentYear = Int()
    
    var startDate = Date()
    var endDate = Date()

    var currentTimePeriod = TimePeriod.Monthly
    
    //MARK:- ViewController LifeCycle
    override func viewDidLoad() {
        super.viewDidLoad()
        
        setmonthFromCurrentmonth(0)
        
        self.ViewselectedIndex.isHidden = true
        //SegmentViewController
        let titleTextAttributes = [NSForegroundColorAttributeName: UIColor.white]
        segmentControlMain.setTitleTextAttributes(titleTextAttributes, for: .selected)
        segmentControlMain.setTitleTextAttributes(titleTextAttributes, for: .normal)


    
        setupSpeandingView()
        
        self.btnNext.addTarget(self, action: #selector(TapTobtnDateNext), for: .touchUpInside)
        self.btnPrevious.addTarget(self, action: #selector(TapTobtnPreviews), for: .touchUpInside)
    }
    
    //MARK:- Other Method
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
    }
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
         let activeUser = UserManager.getActiveUser()
        
        if activeUser?.settings?.time_period == TimePeriod.Monthly.rawValue {
            self.currentTimePeriod = .Monthly
            print("Monthly")
            self.lblweekly.text = "Monthly"
           
            self.Currentmoth = 0
            self.setmonthFromCurrentmonth(self.Currentmoth)
        }
        else if  activeUser?.settings?.time_period == TimePeriod.Weekly.rawValue {
            self.currentTimePeriod = .Weekly
            print("weekly")
            self.lblweekly.text = "weekly"
            self.Currentweek = 0
            self.setWeekFromCurrentWeek(self.Currentweek)
        }
        else if  activeUser?.settings?.time_period == TimePeriod.Yearly.rawValue{
            self.currentTimePeriod = .Yearly
            print("yearly")
            self.lblweekly.text = "yearly"
            self.CurrentYear = 0
            self.setYearFromCurrentYear(self.CurrentYear)
        }
    }
    //MARK:- Uibutton Action
    @IBAction func segmentValueChanged(_ sender: UISegmentedControl) {
        if sender.selectedSegmentIndex == 0 {
            setupSpeandingView()
        }
        else if sender.selectedSegmentIndex == 1 {
            setupTransactionView()
        }
        else if sender.selectedSegmentIndex == 2 {
            setupCategoryView()
        }
        else if sender.selectedSegmentIndex == 3 {
            setupAccountView()
        }
    }
  

    func setupSpeandingView() {
        currentScreen = SelectedScreen.Spending
        
        containerSpending.isHidden = false
        containerTranscation.isHidden = true
        containerCategory.isHidden = true
        containerAccount.isHidden = true
          self.ViewselectedIndex.isHidden = true
        btnTimePeriod.isHidden = false
        btnPrevious.isHidden = false
        btnNext.isHidden = false
        lblTimePeriod.isHidden = false
        btnAccount.isHidden = false
        btnMenu.isHidden = false
        
        btnTimePeriod.setImage(#imageLiteral(resourceName: "date"), for: .normal)
        btnAccount.setImage(#imageLiteral(resourceName: "user"), for: .normal)
        
        for aVC in self.childViewControllers {
            if let vcSpending = aVC as? SpendingVC {
                vcSpending.viewDidAppear(true)
                break
            }
        }
    }
    
    func setupTransactionView() {
        currentScreen = SelectedScreen.Transcation
        
        containerSpending.isHidden = true
        containerTranscation.isHidden = false
        containerCategory.isHidden = true
        containerAccount.isHidden = true
        
        
        btnTimePeriod.setImage(#imageLiteral(resourceName: "date"), for: .normal)
         btnAccount.setImage(#imageLiteral(resourceName: "add"), for: .normal)
          self.ViewselectedIndex.isHidden = true
        btnTimePeriod.isHidden = false
        btnPrevious.isHidden = false
        btnNext.isHidden = false
        lblTimePeriod.isHidden = false
        btnAccount.isHidden = false // img
        btnMenu.isHidden = false
        
        self.selectedCatID = 0
        
        for aVC in self.childViewControllers {
            if let vcTransaction = aVC as? TransactionVC {
                vcTransaction.viewDidAppear(true)
                vcTransaction.DidSelectedRefresh()
                break
            }
        }
    }
    
    func setupCategoryView() {
        currentScreen = SelectedScreen.Category
        
        containerSpending.isHidden = true
        containerTranscation.isHidden = true
        containerCategory.isHidden = false
        containerAccount.isHidden = true
        self.ViewselectedIndex.isHidden = true
        btnTimePeriod.setImage(#imageLiteral(resourceName: "edit"), for: .normal)
        btnAccount.setImage(#imageLiteral(resourceName: "add"), for: .normal)
        
        btnTimePeriod.isHidden = false // img change baaki.
        btnPrevious.isHidden = true
        btnNext.isHidden = true
        lblTimePeriod.isHidden = true
        btnAccount.isHidden = false // img
        btnMenu.isHidden = false
        
        for aVC in self.childViewControllers {
            if let vcCategory = aVC as? CategoryVC {
                vcCategory.viewDidAppear(true)
                vcCategory.DidSelectedRefresh()
                break
            }
        }
    }
    
    func setupAccountView() {
        currentScreen = SelectedScreen.Account
        self.ViewselectedIndex.isHidden = true
        containerSpending.isHidden = true
        containerTranscation.isHidden = true
        containerCategory.isHidden = true
        containerAccount.isHidden = false
        
        btnTimePeriod.setImage(#imageLiteral(resourceName: "edit"), for: .normal)
        btnAccount.setImage(#imageLiteral(resourceName: "add"), for: .normal)

        btnTimePeriod.isHidden = false // img change baaki.
        btnPrevious.isHidden = true
        btnNext.isHidden = true
        lblTimePeriod.isHidden = true
        btnAccount.isHidden = false // img
        btnMenu.isHidden = false
        
        for aVC in self.childViewControllers {
            if let vcAccount = aVC as? AccountVC {
                vcAccount.viewDidAppear(true)
                vcAccount.DidSelectedRefresh()
                break
            }
        }
    }
    
    // MARK: - Button Tap
    @IBAction func TapTobtnDelete(_ sender: Any) {
        if delegateMultiSelect != nil {
            delegateMultiSelect?.didSelectDelete(currentScreen)
        }
    }
    
    @IBAction func TapToBtnedit(_ sender: Any) {
        if delegateMultiSelect != nil {
            delegateMultiSelect?.didSelectEdit(currentScreen)
        }
    }
    
    @IBAction func TapTobtnDone(_ sender: Any) {
        if delegateMultiSelect != nil {
            delegateMultiSelect?.didSelectDone(currentScreen)
        }
    }
    
    @IBAction func didTapBtnTimePeriod(_ sender: UIButton) {
        if currentScreen == .Spending {
            // spending aacount button click
            self.ActionSheetTimePireod()
            //HomeVC.setmonthFromCurrentmonth()
            
            
        }
        else if currentScreen == .Transcation {
            // Open Add transcation screen
            self.ActionSheetTimePireod()
        }
        else if currentScreen == .Category {
            // Open Add category screen
        }
        else if currentScreen == .Account {
            // open add account screen.
        }
    }
    
    @IBAction func didTapBtnAccount(_ sender: UIButton) {
        // Account as well as add button.
        
        if currentScreen == .Spending {
            print("spending aacount button click")
            
            let StoryBord = UIStoryboard(name:"Main", bundle: nil)
            if let UserVc = StoryBord.instantiateViewController(withIdentifier: "PersonalAccountVC") as? PersonalAccountVC {
                
                self.view.addSubview(UserVc.view)
                self.addChildViewController(UserVc)
            }

        }
        else if currentScreen == .Transcation {
            print("Open Add transcation screen")
            
            if let AccontdetaileVc = storyboard?.instantiateViewController(withIdentifier: "AccountDetailVC")as? AccountDetailVC{
                self.navigationController?.pushViewController(AccontdetaileVc, animated: true)
            }
        }
        else if currentScreen == .Category {
            print("Open Add category screen")
            
            let CateGoryDetaileVc = storyboard?.instantiateViewController(withIdentifier: "CategoryDetaileViewController")as! CategoryDetaileViewController
            CateGoryDetaileVc.catType = self.selectedCatType
            self.navigationController?.pushViewController(CateGoryDetaileVc, animated: true)
        }
        else if currentScreen == .Account {
            print("Open add account screen.")
            let CateGoryDetaileVc = storyboard?.instantiateViewController(withIdentifier: "CategoryDetaileViewController")as! CategoryDetaileViewController
             CateGoryDetaileVc.SelectedViewDetaile = true
            self.navigationController?.pushViewController(CateGoryDetaileVc, animated: true)
        }
        
    }
    
    @IBAction func didTapBtnMenu(_ sender: UIButton) {
       // menu view add subview.
        let StoryBord = UIStoryboard(name:"Main", bundle: nil)
        if let menuvc = StoryBord.instantiateViewController(withIdentifier: "MenuVC") as? MenuVC {
            
                self.view.addSubview(menuvc.view)
                self.addChildViewController(menuvc)
        }
        
    }
    //MARK:- ActionSheet method
    func ActionSheetTimePireod(){
        let actionSheetForChangeView: UIAlertController = UIAlertController(title: "SHOW SPENDING", message: nil, preferredStyle: .actionSheet)
        
        let cancelActionButton = UIAlertAction(title: "Cancel", style: .cancel) { _ in
            print("Cancel")
        }
        actionSheetForChangeView.addAction(cancelActionButton)
        
        let saveActionButton = UIAlertAction(title: "Weekly", style: .default)
        { _ in
            self.currentTimePeriod = .Weekly
            self.Currentweek = 0
           self.setWeekFromCurrentWeek(self.Currentweek)
            print("Weekly")
             self.lblweekly.text = "Weekly"
            let activeUser = UserManager.getActiveUser()
            let copyUser = UserManager.copySelf(activeUser!)
            copyUser?.settings?.time_period = TimePeriod.Weekly.rawValue
            
            let result = UserManager.updateUser(copyUser!)
            
            if result.success == false {
                self.showAlert("Alert!", message: result.message)
            }
        }
        actionSheetForChangeView.addAction(saveActionButton)
        
        let deleteActionButton = UIAlertAction(title: "Monthly", style: .default)
        { _ in
            self.currentTimePeriod = .Monthly
            print("Monthly")
             self.lblweekly.text = "Monthly"
            self.Currentmoth = 0
            self.setmonthFromCurrentmonth(self.Currentmoth)
            
            
            let activeUser = UserManager.getActiveUser()
            let copyUser = UserManager.copySelf(activeUser!)
            copyUser?.settings?.time_period = TimePeriod.Monthly.rawValue
            
            let result = UserManager.updateUser(copyUser!)
            
            if result.success == false {
                self.showAlert("Alert!", message: result.message)
            }
            
        }
        actionSheetForChangeView.addAction(deleteActionButton)
        
        let deleteActionButton1 = UIAlertAction(title: "Yearly", style: .default)
        { _ in
            self.currentTimePeriod = .Yearly
            self.CurrentYear = 0
            self.setYearFromCurrentYear(self.CurrentYear)
            print("Yearly")
             self.lblweekly.text = "Yearly"
            
            
            let activeUser = UserManager.getActiveUser()
            let copyUser = UserManager.copySelf(activeUser!)
            copyUser?.settings?.time_period = TimePeriod.Yearly.rawValue
            
            let result = UserManager.updateUser(copyUser!)
            
            if result.success == false {
                self.showAlert("Alert!", message: result.message)
            }
        }
        actionSheetForChangeView.addAction(deleteActionButton1)
        self.present(actionSheetForChangeView, animated: true, completion: nil)
    }
    
    // MARK: - Navigation
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {

    }
    

    
    
    //MARK:- UIbutton Action
    func TapTobtnDateNext(){
        
        if self.currentTimePeriod == .Monthly {
            Currentmoth += 1
            self.setmonthFromCurrentmonth(Currentmoth)
        }
        else if self.currentTimePeriod == .Weekly {
            Currentweek += 1
            self.setWeekFromCurrentWeek(Currentweek)
        }
        else if self.currentTimePeriod == .Yearly {
            CurrentYear += 1
            self.setYearFromCurrentYear(CurrentYear)
        }

    }
    func TapTobtnPreviews(){
        
        if self.currentTimePeriod == .Monthly {
            Currentmoth -= 1
            self.setmonthFromCurrentmonth(Currentmoth)
        }
        else if self.currentTimePeriod == .Weekly {
            Currentweek -= 1
            self.setWeekFromCurrentWeek(Currentweek)
        }
        else if self.currentTimePeriod == .Yearly {
            CurrentYear -= 1
            self.setYearFromCurrentYear(CurrentYear)
        }
    }
    
    
    func getLocalTimeInterval() -> TimeInterval {
        let sourceTimeZone = TimeZone(abbreviation: "GMT")!
        let destinationTimeZone = TimeZone(abbreviation: "\(TimeZone.current.abbreviation()!)")!
        
        let sourceOffset = sourceTimeZone.secondsFromGMT()
        let destinationOffset = destinationTimeZone.secondsFromGMT()
        
        let timeInterval : TimeInterval = TimeInterval(destinationOffset - sourceOffset)
        return timeInterval
    }
    
    func getMonthDates(_ newDate : Date) {
        let calendar = Calendar.current
        
        //Start Date of Month.
        let components = calendar.dateComponents(([.year, .month]), from: newDate)
        let StarofmonthTmp = calendar.date(from: components)
        let Starofmonth = StarofmonthTmp?.addingTimeInterval(getLocalTimeInterval())
        
        //End Date of Month.
        let comps2 = NSDateComponents()
        comps2.month = 1
        comps2.second = -60
        let endofmonth = calendar.date(byAdding: comps2 as DateComponents, to: Starofmonth!)
        
        //Settings.
        self.startDate = Starofmonth!
        self.endDate = endofmonth!
        if timePeriodDelegate != nil {
            timePeriodDelegate?.didSelectTimePeriod(fromDate: Starofmonth!, to_date: endofmonth!)
        }
    }
   
     //MARK:-
    func setmonthFromCurrentmonth(_ monthDifference: Int) {
        let date = Date()
        let mycalendar = Calendar.current
        var myComponents: DateComponents? = mycalendar.dateComponents([.day, .month, .year], from: date)
        let newDate = mycalendar.date(byAdding: (.month), value: monthDifference, to: Date())
        let componentsNewDate: DateComponents? = mycalendar.dateComponents([.month, .year], from: newDate!)
        
        
        let dateFormatter = DateFormatter()
        var monthString: String = ""
        if componentsNewDate?.year == myComponents?.year {
            dateFormatter.dateFormat = "MMMM"
            monthString = dateFormatter.string(from: newDate!).capitalized
            print(monthString)
            print(newDate!)
        }
        else {
            dateFormatter.dateFormat = "MMMM yyyy"
            monthString = dateFormatter.string(from: newDate!).capitalized
            print(monthString)
            print(newDate!)
        }
        self.lblTimePeriod.text = monthString
        
        getMonthDates(newDate!) // for query in database.
    }
    
    func setWeekFromCurrentWeek(_ weekDifference: Int) {
        let totalDaysToSubtractOrAdd: Int = weekDifference * 7
        
        //For Start Day
        let mycalendar = Calendar.current
        var compsToday: DateComponents = mycalendar.dateComponents([.day, .month, .year, .weekday, .hour, .minute, .second], from: Date())

        compsToday.weekday = 2
        compsToday.day = compsToday.day! + totalDaysToSubtractOrAdd
        compsToday.month = compsToday.month!
        compsToday.year = compsToday.year!
        compsToday.hour = 0
        compsToday.minute = 0
        compsToday.second = 1
        
        //For End Date from Start Date.
        let startDateTmp = mycalendar.date(from: compsToday)
        let theStartDate = startDateTmp?.addingTimeInterval(getLocalTimeInterval())
        
        var compsEndDate: DateComponents = DateComponents()
        compsEndDate.day = 7
        compsEndDate.second = -60
        let theEndDate = mycalendar.date(byAdding: compsEndDate, to: theStartDate!)
        
        //Display in label:-
        let dateFormatter11 = DateFormatter()
        dateFormatter11.dateFormat = "dd MMM"
        let firstDateOfWeekstr: String = dateFormatter11.string(from: theStartDate!).capitalized
        let lastDateOfWeekstr: String = dateFormatter11.string(from: theEndDate!).capitalized
        self.lblTimePeriod.text = firstDateOfWeekstr + " - " + lastDateOfWeekstr
        
        //Settings.
        self.startDate = theStartDate!
        self.endDate = theEndDate!
        if timePeriodDelegate != nil {
            timePeriodDelegate?.didSelectTimePeriod(fromDate: self.startDate, to_date: self.endDate)
        }
    }
    
    
    func setYearFromCurrentYear(_ YearDifference:Int){
        let Mydate = Date()
        let mycalendar = Calendar.current
        
        //Start Date of the year.
        var myComponents: DateComponents = mycalendar.dateComponents(([.year, .month, .weekOfYear]), from: Mydate)
        let theYear: Int? = (myComponents.year)! + YearDifference
        let StartDate = "01-01-\(String(theYear!)) 00:00:01"
        let dateFormatter1 = DateFormatter()
        dateFormatter1.dateFormat = "dd-MM-yyyy HH:mm:ss"
        let firstDateyearTmp : Date = dateFormatter1.date(from:StartDate)!
        let firstDateyear : Date = firstDateyearTmp.addingTimeInterval(getLocalTimeInterval())
        
        //Enda date of the year.
        let lastDate = "31-12-\(String(theYear!)) 23:59:59"
        let dateFormatter2 = DateFormatter()
        dateFormatter2.dateFormat = "dd-MM-yyyy HH:mm:ss"
        let lastDateyearTmp :Date = dateFormatter2.date(from:lastDate)!
        let lastDateyear :Date = lastDateyearTmp.addingTimeInterval(getLocalTimeInterval())

        //Settings.
        self.lblTimePeriod.text = String(describing: theYear!)
        self.startDate = firstDateyear
        self.endDate = lastDateyear
        if timePeriodDelegate != nil {
        timePeriodDelegate?.didSelectTimePeriod(fromDate: startDate, to_date: endDate)
        }
        
    }
}
======================******************===========================*************************++++++++++++++++++++++++++


class TransactionVC: UIViewController,UITableViewDelegate,UITableViewDataSource,MultiSelectDelegate, TimePeriodDelegate {
    //MARK:- ViewController Outlet
    
    @IBOutlet weak var viewAddtransaction: UIView!
    @IBOutlet weak var tblViewTransaction: UITableView!
    @IBOutlet weak var lblIncomeAmout: UILabel!
    @IBOutlet weak var lblExpenceAmount: UILabel!
    @IBOutlet weak var lblAccountPersont: UILabel!
    var StrCurrency = ""
    var SelectTypeExpence : CategoryType! = .Expense
    var arrTransactions = [Transaction]()
    var baseVC : BaseViewController!
    var multiselectMode = false
    var longPressRecognizer = UILongPressGestureRecognizer()
    
        var activeUser = User()
    
    private var totalIncome : Double = 0.0
    private var totalExpense : Double = 0.0
    var selectTotalCount = Int()
    //MARK:- ViewController LifeCycle
    override func viewDidLoad() {
        super.viewDidLoad()
    //LongPress Gesture
    longPressRecognizer = UILongPressGestureRecognizer(target: self, action: #selector(longPress))
        self.tblViewTransaction.addGestureRecognizer(longPressRecognizer)
        
        NotificationCenter.default.addObserver(self, selector: #selector(self.updateTranscationList), name: NSNotification.Name(kUserIDChangedNotification), object: nil)
    }
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        self.viewAddtransaction.isHidden = true
        self.viewAddtransaction.CircleRoundsView()
        
        tblViewTransaction.estimatedRowHeight = 100.0
        tblViewTransaction.rowHeight = UITableViewAutomaticDimension
        //CurrenySymbol get
        if let cSymbol = Pref.getObjectForKey(KCurrecySymbol) as? String {
            StrCurrency = cSymbol
        } else {
            StrCurrency = "₹"
        }
        print(StrCurrency)
    }
    
    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        
        
        baseVC = self.parent as! BaseViewController
        baseVC.timePeriodDelegate = self
        
        print(Date().getStartDateOfMonth(Date()))        
        updateTranscationList()
    }
    
    func updateTranscationList() {
        arrTransactions.removeAll()
        if arrTransactions.count > 0 {
           self.viewAddtransaction.isHidden = true
            self.tblViewTransaction.separatorStyle = UITableViewCellSeparatorStyle.singleLine
        }
        else{
            self.viewAddtransaction.isHidden = false
            self.tblViewTransaction.separatorStyle = UITableViewCellSeparatorStyle.none
        }
        
        activeUser = UserManager.getActiveUser()!
        self.lblAccountPersont.text = activeUser.user_name
        if let result = TransactionManager.getTransactions(fromDate: baseVC.startDate,
                                                           toDate: baseVC.endDate,
                                                           catID: baseVC.selectedCatID,
                                                           userID: (activeUser.user_id)) {
            arrTransactions = result as! [Transaction]
            tblViewTransaction.reloadData()
            if result.count > 0 || arrTransactions.count > 0{
                self.viewAddtransaction.isHidden = true
                self.tblViewTransaction.separatorStyle = UITableViewCellSeparatorStyle.singleLine
                
            }
            else{
                self.viewAddtransaction.isHidden = false
                self.tblViewTransaction.separatorStyle = UITableViewCellSeparatorStyle.none
            }
            
        }
       
        tblViewTransaction.reloadData()
        
        totalIncome = 0.0
        totalExpense = 0.0
        for aTrans in arrTransactions {
            if aTrans.category_type == CategoryType.Income.rawValue {
                // label Color Green
                totalIncome += aTrans.amount
            } else {
                // label Color red
                totalExpense += aTrans.amount
            }
        }
        
        lblExpenceAmount.text = StrCurrency + " \(totalExpense)"
        lblIncomeAmout.text = StrCurrency + " \(totalIncome)"
    }
    
    
    //MARK:- LongPress gestur
    func longPress(longPressGestureRecognizer: UILongPressGestureRecognizer) {
        
        if longPressGestureRecognizer.state == UIGestureRecognizerState.began {
            
            let touchPoint = longPressGestureRecognizer.location(in: self.tblViewTransaction)
            if let idxPath = tblViewTransaction.indexPathForRow(at: touchPoint) {
                baseVC.delegateMultiSelect = self
                tblViewTransaction.allowsMultipleSelection = true
                
                multiselectMode = true
                tblViewTransaction.reloadData()
                
                let tttt = DispatchTime.now() + 0.5
                DispatchQueue.main.asyncAfter(deadline: tttt, execute: {
                    self.tblViewTransaction.selectRow(at: idxPath, animated: false, scrollPosition: .none)
                    self.baseVC.ViewselectedIndex.isHidden = false
                })
            }
            else{
              self.baseVC.ViewselectedIndex.isHidden = true
            }
            self.tblViewTransaction.removeGestureRecognizer(longPressGestureRecognizer)
        }
    }

    //MARK:- Uibutton Action
    
    @IBAction func TapTobtnPrevies(_ sender: Any) {
       baseVC.TapTobtnPreviews()
    }
    
    @IBAction func TapTobtnNext(_ sender: Any) {
       baseVC.TapTobtnDateNext()
    }
  
    @IBAction func TapTobtnDownload(_ sender: Any) {
        
    }
    @IBAction func TapTobtnUser(_ sender: Any) {
        let StoryBord = UIStoryboard(name:"Main", bundle: nil)
        
        if let UserVC = StoryBord.instantiateViewController(withIdentifier: "PersonalAccountVC") as? PersonalAccountVC {
            if let basevc = self.parent as? BaseViewController{
                basevc.view.addSubview(UserVC.view)
                basevc.addChildViewController(UserVC)
                UserVC.view.layoutIfNeeded()
            }
        }

    }
    @IBAction func TapTobtnFilter(_ sender: Any) {
        let StoryBord = UIStoryboard(name:"Main", bundle: nil)
        
        if let filtevc = StoryBord.instantiateViewController(withIdentifier: "FilterVC") as? FilterVC {
            if let basevc = self.parent as? BaseViewController{
                basevc.view.addSubview(filtevc.view)
                basevc.addChildViewController(filtevc)
            }
        }
    }
    
    //MARK:- uitableView Method
    func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
        return UITableViewAutomaticDimension
    }

    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return arrTransactions.count
    }
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell: TransactionCell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath) as! TransactionCell
        
        let aTransaction = arrTransactions[indexPath.row]
        cell.lblAmount.text = StrCurrency + " \(aTransaction.amount)"
        cell.lblTrancationname.text =  "\(aTransaction.note)"
        let aCat = CategoryManager.getCategoryByID(aTransaction.category_id)
        cell.imgtransaction.image = UIImage(named: ("Green"  + (aCat?.category_icon)!))
        
        let date = aTransaction.date
        let dateFormatter = DateFormatter()
        dateFormatter.dateFormat = "EEEE, dd MMM yyyy"
        cell.lblDate.text = dateFormatter.string(from: date as Date)
        
       
        
        if aTransaction.category_type == CategoryType.Income.rawValue {
            // label Color Green
          cell.lblAmount.textColor = UIColor(red:23/255.0, green:113/255.0, blue:40/255.0, alpha: 1.0)
        } else {
            // label Color red
            cell.lblAmount.textColor = UIColor(red:239/255.0, green:83/255.0, blue:80/255.0, alpha: 1.0)
        }
        
        if multiselectMode {
            cell.selectionStyle = UITableViewCellSelectionStyle.gray
        }else{
            //self.TblViewTrasaction.addGestureRecognizer(longPressRecognizer)
            cell.selectionStyle = UITableViewCellSelectionStyle.none
        }
        
        return cell
        
    }
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        
      
         self.selectTotalCount += 1
          baseVC.lblselectedindex.text = String(selectTotalCount) + " Selected"
        
        if multiselectMode {
            if (self.tblViewTransaction.indexPathsForSelectedRows?.count)! == 1 {
                baseVC.btnEdit.isHidden = false
                self.tblViewTransaction.addGestureRecognizer(longPressRecognizer)
            } else{
                self.baseVC.btnEdit.isHidden = true
            }
            return
        }
        let AccountVc = storyboard?.instantiateViewController(withIdentifier: "AccountDetailVC")as! AccountDetailVC
        AccountVc.selectedbtnDelete = true
        if let Bean = arrTransactions[indexPath.row] as? Transaction {
            AccountVc.TranBean = Bean
        }
        self.navigationController?.pushViewController(AccountVc, animated: true)
    }
    
    func tableView(_ tableView: UITableView, didDeselectRowAt indexPath: IndexPath) {
        
        self.selectTotalCount -= 1
        baseVC.lblselectedindex.text = String(selectTotalCount) + " Selected"
        
        if multiselectMode {
            if tableView.indexPathsForSelectedRows == nil {
                multiselectMode = false
                tblViewTransaction.allowsMultipleSelection = false
                baseVC.ViewselectedIndex.isHidden = true
                tblViewTransaction.reloadData()
                self.tblViewTransaction.addGestureRecognizer(longPressRecognizer)
                
            }
            
            if tableView.indexPathsForSelectedRows != nil &&  (tableView.indexPathsForSelectedRows?.count)! == 1 {
                self.baseVC.btnEdit.isHidden = false
            } else{
                self.baseVC.btnEdit.isHidden = true
            }
            return
        }
    }
    //MARK:- MutliSelect Delegate Button Tap events.
    
    func didSelectDelete(_ currentScreen: SelectedScreen) {
        if currentScreen == .Transcation {
            //Delete Transcation logic:
            
            if tblViewTransaction.indexPathsForSelectedRows != nil &&
                (tblViewTransaction.indexPathsForSelectedRows?.count)! > 0  {
                
                baseVC.lblselectedindex.text = "0" + " Selected"
                self.selectTotalCount = 0
                
                var arrTranToDelete = [Transaction]()
                for SelectedPath in tblViewTransaction.indexPathsForSelectedRows! {
                    arrTranToDelete.append(arrTransactions[SelectedPath.row])
                }
                let result = TransactionManager.deleteTransactions(arrTranToDelete)
                if result.success == false {
                    self.showAlert("Alert!", message: result.message)
                }
                else {
         
                    updateTranscationList()
                }
            }
            self.DidSelectedRefresh()
        }
    }
    
    func didSelectEdit(_ currentScreen: SelectedScreen) {
        if currentScreen == .Transcation {
            //Edit Transcation logic:
            
            if tblViewTransaction.indexPathsForSelectedRows != nil &&
                (tblViewTransaction.indexPathsForSelectedRows?.count)! > 0  {
                baseVC.lblselectedindex.text = "0" + " Selected"
                self.selectTotalCount = 0
                let selectedIdxPath = tblViewTransaction.indexPathsForSelectedRows!.first!
                let selectedtranObj = arrTransactions[selectedIdxPath.row]
                let Accountvc = storyboard?.instantiateViewController(withIdentifier: "AccountDetailVC")as! AccountDetailVC
                    Accountvc.TranBean = selectedtranObj
                
                self.navigationController?.pushViewController(Accountvc, animated: true)
            }
           self.DidSelectedRefresh()
        }
    }
    
    func didSelectDone(_ currentScreen: SelectedScreen) {
        if currentScreen == .Transcation {
            //Done Transcation logic.
           self.DidSelectedRefresh()
        }
    }
    
    
    //MARK: - Time Period Delegate
    func didSelectTimePeriod(fromDate from_date: Date, to_date: Date) {
       updateTranscationList()
    }
    //Didselectd Button Reload
    func DidSelectedRefresh(){
         baseVC.lblselectedindex.text = "0" + " Selected"
        self.selectTotalCount = 0
        baseVC.ViewselectedIndex.isHidden = true
        self.multiselectMode = false
        self.tblViewTransaction.addGestureRecognizer(longPressRecognizer)
        self.tblViewTransaction.reloadData()
    }
    
    
    
    //MARK:- Other method
    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    


}
==============++++++++++++++++++++++***************************************************************+++++++++++++++
