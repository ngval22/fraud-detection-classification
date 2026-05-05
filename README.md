# Fraud Detection - Machine Learning Classification Problem

[IEEE-CIS Fraud Detection Kaggle Competition](https://www.kaggle.com/competitions/ieee-fraud-detection)

## შესავალი

- კონკურსის მიზანია გადაჭრას თაღლითური ტრანზაქციების პრობლემა. ჩვენი მთავარი ამოცანაა მანქანური სწავლების მოდელით მაქსიმალურად ზუსტად და ეფექტურად შევაფასოთ, რამდენად საეჭვოა ონლაინ ტრანზაქცია და შედეგად გამოვავლინოთ პოტენციური თაღლითური ტრანზაქციები. ამოცანას განსაკუთრებით ართულებს ის, რომ ტრენინგ სეტის მონაცემები დაუბალანსებელია, ანუ ერთი კლასი წარმოდგენილია ბევრად ხშირად, ვიდრე სხვა. ამ ტიპის პრობლემებში ეს მოსალოდნელიცაა, რადგან რეალურ სამყაროში ტრანზაქციების უმეტესობა ლეგიტიმურია.
მონაცემები მოცემულია ორ ფაილად: Transaction Table - ტრანზაქციის მთავარი ინფორმაცია: რაოდენობა, ბარათის ტიპი, კატეგორია, ბანკი და ა.შ., Identity Table - დევაისზე ინფორმაცია, network connection და digital signature ინფორმაცია.
- ამ კონკურსში დიდი მნიშვნელობა აქვს მონაცემების ანალიზს და დამუშავება. პრეპროცესინგისას ერთ-ერთი მთავარი პრობლემაა დაუბალანსებელი მონაცემები. ამ პრობლემის გადასაჭრელად ცალკე run-ად ვტესტავ undersampling მიდგომას, ანუ რენდომულად ვშლი სტრიქონებს, სადაც isFraud=false, სანამ დატა რაღაც დონეზე არ დაბალანსდება. ასევე ვტესტავ `class_weight='balanced'` მიდგომასაც, რომ ვნახო რომელი უკეთეს შედეგს იძლევა. მოდელის შერჩევისას თავდაპირველი გეგმაა, რომ დავატრენინგო ჯერ Logistic Regression მოდელი, შემდეგ XGBoost და ბოლოს RandomForest. ამ მიდგომით გამოვცდი სამ მთავარ მეთოდოლოგიას: ცალკე აღებული ძლიერი მოდელი, boosting: სუსტი მოდელები, ყოველი მომდევნო მოდელი არსებულების შედეგს გააუმჯობესებს და bagging: ცალ-ცალკე დატრენინგებული მოდელები მაღალილ ვარიაციით, რომლებიც ბოლოს გასაშუალოვდება ვარიაციის შესამცირებლად.

## რეპოზიტორიის სტრუქტურა

- model-experiment-logistic-regression.ipynb - Logistic Regression არქიტექტურის ექსპერიმენტები. ამ ფაილში ცალ-ცალკეა გამოყოფილი Cleaning, Feature Engineering, Feature Selection და Training ნაწილები.
- model-experiment-XGBoost.ipynb - XGBoost არქიტექტურის ექსპერიმენტები იგივე სტრუქტურით. preprocessing მსგავსია, მაგრამ feature selection და training უკვე XGBoost-ზეა მორგებული.
- model-experiment-RandomForest.ipynb - RandomForest ექსპერიმენტი.
- model_inference.ipynb - საბოლოოდ საუკეთესო მოდელით ტესტ სეტზე პროგნოზების გაკეთებისთვის.
- README.md - პროექტის მიდგომების და შედეგების აღწერა.

## Data Analysis

<img width="1189" height="390" alt="eda_class_balance" src="https://github.com/user-attachments/assets/7b876e40-efc0-4096-b628-af0ccd1611dd" />
როგორც მარცხენა გრაფზე ჩანს დატასეტში მოცემული გვაქვს 569,877 ლეგიტიმური ტრანზაქცია და მხოლოდ 20,663 თაღლითური. დაახლოებით ~3.5% ია ყველა ტრანზაქციიდან თაღლითური. ესაა სწორედ შესავალში აღნიშნული დაუბალანსებელი მონაცემების პრობლემა. მოდელი თუ ყოველთვის დადებით პასუხს დააბრუნებს, 96.5% იქნება მისი accuracy მეტრიკა, თუმცა ცხადია რომ ეს მოდელი არ გამოდგება. აქ ვხედავთ სწორედ, თუ რატომ არის მნიშვნელოვანი undersampling დასაბალანსებლად და სხვა მეტრიკების გამოყენება მაგალითად, AUC-ROC ან Average Precision.

მარჯვენა გრაფზე კი მოცემულია თაღლითური და ლეგიტიმური ტრანზაქციების განაწილებები, ტრანზაქციის ოდენობის(TransactionAmt) მიხედვით. გრაფიდან ჩანს რომ თაღლითური ტრანზაქციები უფრო გაშლილია და ნაკლები სპაიკები აქვს. 

<img width="989" height="290" alt="eda_fraud_by_hour" src="https://github.com/user-attachments/assets/fb082531-1d51-43e3-a824-dbbf36b4704e" />
მონაცემების ანალიზისას ასევე შევხედე თაღლითური ტრანზაქციების განაწილება საათის მიხედვით. როგორც სურათზე ჩანს ასეთი ტრანზაქციები მეტია დილის მონაკვეთში და შესამჩნევი პიკი აქვს დილის 7-8 საათზე.

## Cleaning

პირდაპირ ვდოპავ ისეთ column-ებს,რომლებსაც 50%-ზე მეტი აქვთ გამოტოვებული მნიშვნელობა. ეს მახასიათებლები საკმარის ინფორმაციას მაინც არ მისცემს მოდელს და შევსებით შეიძლება უფრო მეტი ხმაური შეიტანოს. მითუმეტეს გამართლებულია ეს მიდგომა როცა 400-ზე მეტი ცვლადი გვაქვს.

## Feature Engineering

- პირველ რიგში TransactionAmt მოვდებ ლოგარითმს, რადგან ერთეულმა დიდმა ტრანზაქციებმა შეიძლება მოდელი დააბნიოს და ხმაური შემოიტანოს, ლოგარითმის მოდებით განაწილება ნორმალურს უახლოვდება და მოდელისთვის ბევრად უფრო ხელსაყრელია. ასევე ცალკე ცვლადად გავიტანე ცენტების რაოდენობა TransactionAmt_cents. ეს შეიძლება მოდელისთვის ძლიერი ინფორმატორი იყოს, რადგან თაღლითურ ტრანზაქციებში ხშირად არის რაღაც პატერნი მაგ. 0.5, 0.99, თაღლითები ხშირად ირჩევენ ისეთ რიცხვს რომ მაქსიმალურად ბუნებრივი იყოს ტრანზაქციისთვის, ნამდვილი ტრანზაქციები კი ხშირად სხვადასხვა საკომისიოების გამო უფრო რენდომულად ჩანს. ამავე მიზეზით შემოვიტანე TransactionAmt_is_round.
- ასევე ტრანზაქციის შესრულების დროიდან გამოვიტანე შემდეგი ცვლადები: hour, day_of_week, is_weekend. რადგან სამივე ეს მახასიათებელი მნიშვნელოვან სიგნალს გვაძლევს ტრანზაქციის თაღლითურობასთან. 
- კატეგორიული ცვლადები გადავიყვანე რიცხვით ფორმატში OrdinalEncoder-ით. ეს საჭიროა Logistic Regression-ისთვისაც და XGBoost-ისთვისაც, რადგან pipeline-ში ორივე მოდელი numeric input-ს იღებს. Encoder-ს აქვს unseen category handling, რომ ტესტ სეტზე ახალი კატეგორია რომ შეგვხვდეს, pipeline არ გაფუჭდეს.
- ასევე რამდენიმე მნიშვნელოვან numeric column-ზე დავამატე missing indicator-ები. ზოგჯერ თვითონ ის ფაქტი, რომ მნიშვნელობა გამოტოვებულია, შეიძლება მოდელისთვის სასარგებლო სიგნალი იყოს.
- TransactionDT დროითი feature-ების შექმნის შემდეგ იშლება, რომ მოდელმა პირდაპირ დროის ინდექსი არ ისწავლოს.
- XGBoost-ისთვის და RandomForest-ისთვის scaling არ გამომიყენებია, რადგან ხის მოდელებს feature-ების მასშტაბს ჰენდლავს Logistic Regression-ისგან განსხვავებით. Logistic Regression-ში კი scaling საჭიროა.

## Feature Selection

- Logistic Regression notebook-ში feature selection გავაკეთე სამ ეტაპად. ჯერ კორელაციის ფილტრით ვშლი ძალიან მსგავს ცვლადებს, რადგან ასეთი feature-ები Logistic Regression-ს ზედმეტად აბნევს და multicollinearity-ს ქმნის. შემდეგ VarianceThreshold-ით ვშლი ისეთ ცვლადებს, სადაც ინფორმაცია თითქმის არ იცვლება. ბოლოს RFE-ს ვიყენებ Logistic Regression estimator-ით და ვტოვებ ყველაზე სასარგებლო 30 ცვლადს.
- XGBoost notebook-ში feature selection ცოტა სხვანაირია. ჯერ ისევ ვიყენებ correlation filter-ს და VarianceThreshold-ს, მაგრამ ბოლოს RFE-ის ნაცვლად XGBoost-ის feature importance-ს ვიყენებ და ვტოვებ 80 საუკეთესო ცვლადს. ეს უფრო ბუნებრივია XGBoost-ისთვის, რადგან ხის მოდელს თვითონ შეუძლია დაინახოს რომელი ცვლადები ამცირებს loss-ს.
- RandomForest notebook-შიც feature selection იმავე ლოგიკითაა გაკეთებული. correlation filter-ის და VarianceThreshold-ის შემდეგ ვიყენებ RandomForestClassifier-ის feature importance-ს და ვტოვებ top 80 feature-ს. ეს უკეთ ერგება RandomForest-ს, ვიდრე Logistic Regression-ის RFE. selector fit პაიპლაინის შიგნით ხდება, ამიტომ კროს ვალიდაციისას არჩეული feature-ები მხოლოდ შესაბამისი train fold-იდან ისწავლება, რომ არ გაილიქოს.

## Training
- Logistic Regression-ზე შევადარე რამდენიმე ვარიანტი: baseline Logistic Regression, balanced class weights, RandomUnderSampler, L1 regularization, ძლიერი L2 regularization underfitting-ის შესამოწმებლად, სუსტი L2 regularization overfitting-ის შესამოწმებლად და ElasticNet.
undersampling არ არის ყველა Logistic Regression run-ის საერთო ნაწილი. ის გამოყენებულია მხოლოდ ერთ ექსპერიმენტში, LR_03_UnderSampling, ხოლო სხვა run-ებში ან საერთოდ არ ხდება დაბალანსება, ან გამოიყენება class_weight='balanced'.
- Logistic Regression-ის საუკეთესო შედეგი მიიღო LR_02_Balanced_Weights მოდელმა: validation ROC-AUC = 0.81111. თუმცა top მოდელები ძალიან ახლოს იყო ერთმანეთთან: weak L2, ElasticNet და L1 პრაქტიკულად იგივე შედეგს აჩვენებს. ეს სავარაუდოდ იმით არის გამოწვეული, რომ ყველა მათგანი იგივე preprocessing-ს და იგივე linear decision boundary-ს იყენებს. Undersampling ოდნავ ჩამორჩა, იმიტომ რომ სავარაუდოდ ბევრი ლეგიტიმური ტრანზაქცია იკარგება.
- XGBoost-ზე შევადარე weighted baseline, shallow underfit მოდელი, deeper overfit მოდელი, undersampling ვარიანტი და უფრო regularized ვარიანტი. დაუბალანსებელი კლასებისთვის ძირითადად ვიყენებ scale_pos_weight-ს, რაც XGBoost-სთვის უფრო მორგებული მიდგომაა. undersampling აქაც მხოლოდ ერთი შედარებითი ექსპერიმენტია: XGB_04_UnderSampling.
- შეფასებისთვის ვიყენებ ROC-AUC-ს, რადგან დატა ძალიან დაუბალანსებელია და accuracy ამ პრობლემისთვის კარგი მეტრიკა არ არის. ასევე ვადარებ train და validation AUC-ს შორის სხვაობას, რომ დავინახო overfitting ან underfitting.
- საბოლოო pipeline ინახება მთლიანად: cleaning, feature engineering, feature selection, imputation და თვითონ მოდელი. Logistic Regression-ში დამატებით არის scaling, XGBoost-ში კი scaling არ არის. ეს მნიშვნელოვანია, რადგან pipeline პირდაპირ raw test set-ზე უნდა გაეშვას და ცალკე ხელით preprocessing არ უნდა დასჭირდეს.

### Hyperparameter Optimization მიდგომა

- ამ პროექტში არ გამომიყენებია grid search, რადგან მონაცემები დიდია და თითოეული pipeline run საკმაოდ მძიმეა: preprocessing, feature selection და cross-validation თავიდან სრულდება. ამიტომ გამოვიყენე კონტროლირებული მიდგომა, სადაც თითოეული run კონკრეტულ ჰიპოთეზას ამოწმებს: baseline, class imbalance-ის დამუშავება, underfitting-check, overfitting-check და regularization.
- Logistic Regression-ში მთავარი შესამოწმებელი პარამეტრები იყო class_weight, use_undersampling, penalty, C, solver, l1_ratio და max_iter. აქ მიზანი იყო მენახა, linear მოდელს რამდენად ეხმარება imbalance-ის დამუშავება და regularization. შედეგები ძალიან ახლოს გამოვიდა, რაც ნიშნავს, რომ მოდელის ლიმიტი უფრო feature space-იდან და linear decision boundary-დან მოდის, ვიდრე კონკრეტული C მნიშვნელობიდან.
- XGBoost-ში შევცვალე n_estimators, max_depth, learning_rate, min_child_weight, reg_lambda, subsample, colsample_bytree, scale_pos_weight და use_undersampling. აქ მიდგომა იყო capacity-ს კონტროლი: shallow მოდელი underfitting-ისთვის, deeper მოდელი მაღალი capacity-ის შესამოწმებლად, ხოლო regularized მოდელი იმისთვის, რომ მენახა overfit gap შემცირდებოდა თუ არა validation score-ის დიდი დაკარგვის გარეშე.
- RandomForest-ში შევადარე n_estimators, max_depth, min_samples_leaf, max_features, class_weight და use_undersampling. unrestricted forest-ებმა თითქმის იდეალური train ROC-AUC აჩვენა, ამიტომ შემდეგი run-ები მიმართული იყო overfitting-ის შემცირებაზე: depth-ის შეზღუდვა, leaf size-ის გაზრდა და max_features='sqrt'.
- საბოლოო არჩევანისთვის მხოლოდ ერთი მეტრიკა არ გამოვიყენე. ვადარებდი cv_val_roc_auc_mean-ს, cv_train_roc_auc_mean-ს, overfit_gap-ს და დამატებულ time_holdout_roc_auc-ს. თუ მოდელს მაღალი shuffled CV ჰქონდა, მაგრამ time holdout-ზე მკვეთრად ეცემოდა, ასეთ შედეგს ნაკლებად სანდოდ ჩავთვლიდი.

### მოდელის არჩევა
<img width="800" height="281" alt="image" src="https://github.com/user-attachments/assets/e1e0d382-0cae-4948-8adf-da59f4e28831" />

- RandomForest-ზე საუკეთესო შედეგი მიიღო RF_02_Balanced_Weights მოდელმა: validation ROC-AUC = 0.93721, std = 0.00171. იგივე validation score აჩვენა RF_04_Deeper_Overfit_Check-მაც, მაგრამ ორივე run-ში unrestricted forest-ის გამო train score ძალიან მაღალია და overfitting-ის რისკი რჩება. შემდეგ მოდის RF_01_Baseline ROC-AUC = 0.93261, RF_05_UnderSampling ROC-AUC = 0.92609, RF_06_Regularized ROC-AUC = 0.90654, ხოლო RF_03_Shallow_Underfit ROC-AUC = 0.85257. ეს შედეგები კარგად აჩვენებს bias-variance tradeoff-ს: shallow მოდელი ზედმეტად მარტივია, regularized მოდელი overfitting-ს ამცირებს მაგრამ ქულას კარგავს, ხოლო balanced unrestricted forest საუკეთესო RF ვარიანტია.
- inference notebook-ში გამოსაყენებლად საუკეთესო კანდიდატად ავირჩიე XGBoost pipeline, რადგან არსებული run-ების მიხედვით ყველაზე მაღალი validation ROC-AUC სწორედ XGBoost-მა აჩვენა. საუკეთესოიყო XGB_03_Deeper_Overfit_Check, რომლის shuffled CV validation ROC-AUC იყო `0.95770`.
- ეს run overfitting-ის ნიშნებს აჩვენებს, რადგან train ROC-AUC იყო 0.98890, ხოლო validation ROC-AUC 0.95770, ანუ gap არის დაახლოებით 0.03120. მიუხედავად ამისა, ის მაინც საუკეთესო კანდიდატია, რადგან overfitting არ ნიშნავს ავტომატურად ცუდ მოდელს: ღრმა XGBoost-მა რეალური არალინეარული თაღლითური ნიმუშებიც უკეთ ისწავლა და validation-ზე სხვა XGBoost ვარიანტებს აჯობა.
- საბოლოო გადაწყვეტილება დამოკიდებულია დამატებულ time_holdout_roc_auc-ზეც. იმავე მოდელს აქვს საუკეთესო time-holdout AUC: 0.89670. ასე რომ, XGB_03_Deeper_Overfit_Check-ის საუკეთესო XGBoost მოდელად დატოვება გამართლებულია.
- Logistic Regression ყველაზე ცუდი შედეგი დადო, რადგან მისი საუკეთესო ROC-AUC დაახლოებით `0.81111` იყო და XGBoost-ს მნიშვნელოვნად ჩამორჩება. RandomForest ბევრად ძლიერი აღმოჩნდა Logistic Regression-ზე და საუკეთესო RF run-მა (`RF_02_Balanced_Weights`) ROC-AUC = `0.93721` მიიღო, მაგრამ XGBoost-ის საუკეთესო validation score-ს (`0.95770`) მაინც ჩამორჩა. ამიტომ საბოლოო inference-ის მთავარი კანდიდატი რჩება XGBoost.


- XGBoost-მა Kaggle submission ზე მიიღო ROC-AUC = `0.865007`. ეს ნორმალური შედეგია, მოდელი აშკარად სწავლობს fraud signal თუმცა ეს score მნიშვნელოვნად დაბალია local shuffled CV შედეგზე (`0.95770`). ეს სხვაობა აჩვენებს, რომ shuffled cross-validation ზედმეტად ოპტიმისტური იყო. IEEE-CIS Fraud Detection-ში train და test მონაცემები დროით არის გაყოფილი, ხოლო shuffled CV ადრეულ და გვიან ტრანზაქციებს ერთმანეთში ურევს. ამიტომ მოდელმა შეიძლება ისწავლოს ისეთი pattern-ები, რომლებიც training period-ში კარგად მუშაობს, მაგრამ Kaggle test period-ზე სუსტდება.

## MLflow Tracking
https://dagshub.com/ngval22/fraud-detection-classification.mlflow/#/experiments
- MLflow-ზე Logistic Regression-ისთვის შექმნილია ცალკე ექსპერიმენტი: `Logistic_Regression_Training`.
- MLflow-ზე XGBoost-ისთვისაც შექმნილია ცალკე ექსპერიმენტი: `XGBoost_Training`.
- MLflow-ზე RandomForest-ისთვისაც შექმნილია ცალკე ექსპერიმენტი: `RandomForest_Training`.
- ყველა ექსპრიმენტში ცალკე run-ებია Cleaning, Feature Engineering და Feature Selection ეტაპებისთვის, სადაც დავლოგე გამოყენებული მიდგომები და ძირითადი პარამეტრები. Training run-ებში ვლოგავ მოდელის ჰიპერპარამეტრებს და cross-validation მეტრიკებს.
- დალოგილი ძირითადი მეტრიკებია: `cv_val_roc_auc_mean`, `cv_val_roc_auc_std`, `cv_train_roc_auc_mean`, `overfit_gap`, `time_holdout_roc_auc` და fold-level AUC-ები.
- საუკეთესო Logistic Regression pipeline ინახება MLflow-ში სახელით `LR_BestPipeline`, საუკეთესო XGBoost pipeline ინახება სახელით `XGB_BestPipeline`, ხოლო RandomForest-ის საუკეთესო pipeline ინახება `RF_BestPipeline` სახელით. საბოლოო Kaggle submission-ისთვის `model_inference.ipynb` Model Registry-დან ჩატვირთავს საბოლოოდ შერჩეულ საუკეთესო pipeline-ს, ამჟამინდელი ანალიზით კი მთავარი კანდიდატია XGBoost.

## აღმოჩენილი პრობლემები და გამოსწორება

- ექსპერიმენტების ანალიზისას შევამჩნიე, რომ მხოლოდ shuffled StratifiedKFold validation შეიძლება ზედმეტად ოპტიმისტური იყოს, რადგან IEEE-CIS მონაცემებში TransactionDT დროით მიმდევრობას ასახავს და train/test განაწილებები შეიძლება დროში იცვლებოდეს. ამის გამო სამივე notebook-ში დავამატე დამატებითი time_holdout_roc_auc მეტრიკა: მოდელი train-ის პირველ 80%-ზე, დროის მიხედვით, სწავლობს და ბოლო 20%-ზე ფასდება. shuffled CV მაინც დავტოვე შედარებისთვის, მაგრამ time-based holdout უკეთ აჩვენებს, რამდენად სტაბილურია მოდელი დროს შეცვლაზე.

