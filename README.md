# housing_xboost_python{
 "cells": [
  {
   "cell_type": "code",
   "execution_count": 84,
   "metadata": {},
   "outputs": [],
   "source": [
    "### Steps\n",
    "# Preprocess data in R \n",
    "# Load in data\n",
    "# Create training and testing data\n",
    "# Run model (figure out cv search and how to alter this)\n",
    "# Run model with random cv search\n",
    "# Figure out how to evaluate the model (get multiple measures at once)\n",
    "# Save the model\n",
    "# Export the predicted values\n",
    "# Load in the model and run again with new data from FHHC\n",
    "\n",
    "import pandas as pd\n",
    "import numpy as np\n",
    "import xgboost as xgb\n",
    "from xgboost import XGBClassifier\n",
    "from xgboost.sklearn import XGBClassifier\n",
    "from sklearn.model_selection import RandomizedSearchCV"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 85,
   "metadata": {},
   "outputs": [],
   "source": [
    "house_dat = pd.read_csv(\"house_dat_8_13_20.csv\", delimiter = \",\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 39,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Quarter</th>\n",
       "      <th>Gender</th>\n",
       "      <th>RaceWhite</th>\n",
       "      <th>RaceBlack</th>\n",
       "      <th>Agegroup</th>\n",
       "      <th>OverallHealth</th>\n",
       "      <th>CapableManagingHealthCareNeeds</th>\n",
       "      <th>HandlingDailyLife</th>\n",
       "      <th>ControlLife</th>\n",
       "      <th>DealWithCrisis</th>\n",
       "      <th>...</th>\n",
       "      <th>EverServed</th>\n",
       "      <th>ActiveDuty_Else</th>\n",
       "      <th>NightsHomeless</th>\n",
       "      <th>aiety</th>\n",
       "      <th>mdd_r</th>\n",
       "      <th>mdd_s</th>\n",
       "      <th>another_s_ident</th>\n",
       "      <th>drug_use</th>\n",
       "      <th>er_hos_use_base</th>\n",
       "      <th>jail_arrest_base</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>8.0</td>\n",
       "      <td>4.0</td>\n",
       "      <td>3.0</td>\n",
       "      <td>2.0</td>\n",
       "      <td>3.0</td>\n",
       "      <td>2.0</td>\n",
       "      <td>...</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>9.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>6.0</td>\n",
       "      <td>1.0</td>\n",
       "      <td>1.0</td>\n",
       "      <td>4.0</td>\n",
       "      <td>4.0</td>\n",
       "      <td>4.0</td>\n",
       "      <td>...</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>12.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>12.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>8.0</td>\n",
       "      <td>2.0</td>\n",
       "      <td>1.0</td>\n",
       "      <td>2.0</td>\n",
       "      <td>4.0</td>\n",
       "      <td>4.0</td>\n",
       "      <td>...</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>26.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>9.0</td>\n",
       "      <td>4.0</td>\n",
       "      <td>2.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>7.0</td>\n",
       "      <td>4.0</td>\n",
       "      <td>2.0</td>\n",
       "      <td>2.0</td>\n",
       "      <td>2.0</td>\n",
       "      <td>4.0</td>\n",
       "      <td>...</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>30.0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>9.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>7.0</td>\n",
       "      <td>2.0</td>\n",
       "      <td>1.0</td>\n",
       "      <td>5.0</td>\n",
       "      <td>5.0</td>\n",
       "      <td>5.0</td>\n",
       "      <td>...</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>12.0</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "<p>5 rows × 54 columns</p>\n",
       "</div>"
      ],
      "text/plain": [
       "   Quarter  Gender  RaceWhite  RaceBlack  Agegroup  OverallHealth  \\\n",
       "0        2       1          0          1       8.0            4.0   \n",
       "1        1       0          1          0       6.0            1.0   \n",
       "2        1       0          1          0       8.0            2.0   \n",
       "3        1       1          1          0       7.0            4.0   \n",
       "4        2       0          0          1       7.0            2.0   \n",
       "\n",
       "   CapableManagingHealthCareNeeds  HandlingDailyLife  ControlLife  \\\n",
       "0                             3.0                2.0          3.0   \n",
       "1                             1.0                4.0          4.0   \n",
       "2                             1.0                2.0          4.0   \n",
       "3                             2.0                2.0          2.0   \n",
       "4                             1.0                5.0          5.0   \n",
       "\n",
       "   DealWithCrisis  ...  EverServed  ActiveDuty_Else  NightsHomeless  aiety  \\\n",
       "0             2.0  ...           0                1             0.0      0   \n",
       "1             4.0  ...           0                0            12.0      0   \n",
       "2             4.0  ...           0                0            26.0      0   \n",
       "3             4.0  ...           0                1            30.0      0   \n",
       "4             5.0  ...           0                1             0.0      0   \n",
       "\n",
       "   mdd_r  mdd_s  another_s_ident  drug_use  er_hos_use_base  jail_arrest_base  \n",
       "0      0      0                0       9.0              0.0               0.0  \n",
       "1      0      0                1      12.0              0.0               0.0  \n",
       "2      0      0                0       9.0              4.0               2.0  \n",
       "3      1      0                0       9.0              0.0               0.0  \n",
       "4      0      0                0      12.0              0.0               0.0  \n",
       "\n",
       "[5 rows x 54 columns]"
      ]
     },
     "execution_count": 39,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "house_dat.head()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 86,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "(2101, 54)"
      ]
     },
     "execution_count": 86,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "house_dat.shape"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 87,
   "metadata": {},
   "outputs": [],
   "source": [
    "### Break into testing and training\n",
    "from sklearn.model_selection import train_test_split\n",
    "y = house_dat[[\"Housing.y\"]]\n",
    "predictors = house_dat.drop([\"Housing.y\"], axis = 1)\n",
    "### Need to make this a horitzontal array not sure why\n",
    "y = y.values.ravel()\n",
    "predictors = predictors.to_numpy()\n",
    "X_train, X_test, y_train, y_test = train_test_split(predictors, y, test_size = .20, random_state = 53)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 88,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([1, 1, 0, ..., 1, 1, 1], dtype=int64)"
      ]
     },
     "execution_count": 88,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "y_train"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 116,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([1, 0, 1, 1, 0, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1, 0, 1, 1, 0, 1,\n",
       "       1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0,\n",
       "       1, 1, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 1, 1, 1,\n",
       "       1, 1, 1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 0, 1,\n",
       "       1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 1,\n",
       "       1, 1, 1, 1, 1, 1, 0, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,\n",
       "       1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 0, 0, 1, 1, 1, 0, 1, 0, 1, 1,\n",
       "       1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 1, 0, 0, 1, 1, 1, 1,\n",
       "       1, 0, 0, 1, 1, 1, 0, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1,\n",
       "       1, 1, 0, 1, 0, 0, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,\n",
       "       1, 0, 1, 1, 1, 0, 1, 1, 1, 0, 1, 1, 1, 1, 1, 0, 0, 1, 1, 1, 0, 1,\n",
       "       1, 1, 0, 0, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 0, 1, 1, 1, 1, 0,\n",
       "       1, 1, 1, 1, 1, 0, 1, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 1, 1, 1,\n",
       "       1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 1, 0, 1, 1, 1,\n",
       "       1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 0,\n",
       "       1, 1, 1, 0, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1,\n",
       "       1, 1, 1, 0, 1, 1, 1, 1, 1, 0, 0, 1, 0, 1, 1, 0, 1, 0, 1, 1, 1, 1,\n",
       "       0, 1, 1, 1, 0, 1, 0, 1, 1, 1, 0, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1,\n",
       "       1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 0, 1, 0, 0,\n",
       "       1, 1, 1], dtype=int64)"
      ]
     },
     "execution_count": 116,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "### Figure out how to get set up a Random search for xgboost (should be the same as previous random search)\n",
    "from sklearn.model_selection import RandomizedSearchCV\n",
    "xclas = XGBClassifier()  # and for classifier  \n",
    "xclas.fit(X_train, y_train)  \n",
    "pred_xclas =  xclas.predict(X_test)\n",
    "pred_xclas"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 114,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "{'fit_time': array([0.15203905, 0.14604545, 0.14196086, 0.15003562, 0.1440444 ]),\n",
       " 'score_time': array([0.0030129 , 0.00198865, 0.00399923, 0.00200105, 0.00299549]),\n",
       " 'test_accuracy': array([0.86011905, 0.86607143, 0.85416667, 0.86011905, 0.83333333]),\n",
       " 'test_precision': array([0.8650519 , 0.88970588, 0.86363636, 0.88321168, 0.86545455]),\n",
       " 'test_balanced_accuracy': array([0.73449612, 0.7809437 , 0.7337093 , 0.76828548, 0.7288578 ])}"
      ]
     },
     "execution_count": 114,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "from sklearn.model_selection import cross_validate\n",
    "from sklearn.metrics import confusion_matrix\n",
    "scoring = ['accuracy', 'precision', 'balanced_accuracy']\n",
    "cv_results = cross_validate(xclas.fit(X_train, y_train) , X_train, y_train, cv=5, scoring=scoring)\n",
    "cv_results"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 145,
   "metadata": {},
   "outputs": [],
   "source": [
    "### Get a confusion matrix and then get sensitivity by hand\n",
    "con_mat = confusion_matrix(y_test, pred_xclas)\n",
    "#sensitivity <- TP / (TP + FN)\n",
    "#specificity <- TN / (TN + FP)\n",
    "con_mat\n",
    "#tn, fp, fn, tp\n",
    "sensitivity =  con_mat[1][1] / (con_mat[1][1] + con_mat[1][0])\n",
    "specificity = con_mat[0][0] / (con_mat[0][0] + con_mat[0][1])\n",
    "### \n",
    "balanced_accuracy =  (sensitivity+specificity)/2\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 231,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>sensitivity</th>\n",
       "      <th>specificity</th>\n",
       "      <th>balanced_accuracy</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0.931373</td>\n",
       "      <td>0.591304</td>\n",
       "      <td>0.761338</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   sensitivity  specificity  balanced_accuracy\n",
       "0     0.931373     0.591304           0.761338"
      ]
     },
     "execution_count": 231,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "### Fix this later\n",
    "sensitivity = pd.DataFrame(sensitivity, columns = [\"sensitivity\"], index = [0])\n",
    "specificity = pd.DataFrame(specificity, columns = [\"specificity\"], index = [0])\n",
    "balanced_accuracy = pd.DataFrame(balanced_accuracy, columns = [\"balanced_accuracy\"], index = [0])\n",
    "\n",
    "frames = [sensitivity, specificity, balanced_accuracy]\n",
    "metrics_dat = pd.concat(frames, axis = 1)\n",
    "metrics_dat"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 174,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>0</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>count</th>\n",
       "      <td>2101.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>mean</th>\n",
       "      <td>0.757734</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>std</th>\n",
       "      <td>0.428556</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>min</th>\n",
       "      <td>0.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>25%</th>\n",
       "      <td>1.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>50%</th>\n",
       "      <td>1.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>75%</th>\n",
       "      <td>1.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>max</th>\n",
       "      <td>1.000000</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "                 0\n",
       "count  2101.000000\n",
       "mean      0.757734\n",
       "std       0.428556\n",
       "min       0.000000\n",
       "25%       1.000000\n",
       "50%       1.000000\n",
       "75%       1.000000\n",
       "max       1.000000"
      ]
     },
     "execution_count": 174,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Figure out good values for these\n",
    "## sum(negative instances) / sum(positive instances)\n",
    "## Good for unbalanced weights\n",
    "#Description of the parameters: https://xgboost.readthedocs.io/en/latest/parameter.html\n",
    "y_panda = pd.DataFrame(y)\n",
    "y_panda.describe()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 190,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Develop develop one for random grid search\n",
    "params = {  \n",
    "    \"max_depth\": [6,12,15, 18],\n",
    "    \"eta\": [.01, .001, .3],\n",
    "    \"colsample_bytree\": [.5, .6, .7],\n",
    "    \"subsample\": [.5, .6, .7],\n",
    "    'lambda': [1, 1.1, 1.2, 1.3, 1.4],\n",
    "    'alpha': [0, .1, .2, .3],\n",
    "    \"scale_pos_weight \": [.25, .5, 1],\n",
    "}"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 191,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "RandomizedSearchCV(cv=10, estimator=XGBClassifier(), n_jobs=-1,\n",
       "                   param_distributions={'alpha': [0, 0.1, 0.2, 0.3],\n",
       "                                        'colsample_bytree': [0.5, 0.6, 0.7],\n",
       "                                        'eta': [0.01, 0.001, 0.3],\n",
       "                                        'lambda': [1, 1.1, 1.2, 1.3, 1.4],\n",
       "                                        'max_depth': [6, 12, 15, 18],\n",
       "                                        'scale_pos_weight ': [0.25, 0.5, 1],\n",
       "                                        'subsample': [0.5, 0.6, 0.7]})"
      ]
     },
     "execution_count": 191,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "xclas_random = XGBClassifier()\n",
    "gs = RandomizedSearchCV(xclas_random, params, n_jobs=-1, cv = 10)  \n",
    "gs.fit(X_train, y_train)  "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 213,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0.8720238095238095"
      ]
     },
     "execution_count": 213,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "#best_estimator_\n",
    "#best_score_ \n",
    "#best_params_\n",
    "gs.best_estimator_\n",
    "gs.best_score_"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 207,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([1, 0, 1, 1, 0, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 0, 1, 1, 0, 0,\n",
       "       1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 1,\n",
       "       1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 0, 1, 1, 1,\n",
       "       1, 1, 1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 0, 1,\n",
       "       1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 1, 0, 1,\n",
       "       1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,\n",
       "       1, 1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 0, 1, 0, 1, 1,\n",
       "       1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 1, 1, 0, 1, 1, 1, 1,\n",
       "       1, 1, 0, 1, 1, 1, 0, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1,\n",
       "       1, 1, 0, 1, 0, 1, 1, 0, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1,\n",
       "       1, 0, 1, 1, 1, 0, 1, 1, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 0, 1,\n",
       "       1, 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 1, 1, 0, 0, 1, 1, 1, 1, 1,\n",
       "       1, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 0, 0, 1, 1, 1, 0, 1, 1, 1,\n",
       "       1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 1, 0, 1, 1, 1,\n",
       "       1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 0,\n",
       "       1, 1, 1, 0, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 1, 0, 1,\n",
       "       1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 0, 1, 0, 1, 1, 1, 0,\n",
       "       0, 1, 1, 1, 0, 1, 0, 0, 1, 1, 0, 1, 1, 1, 0, 1, 1, 1, 1, 1, 1, 1,\n",
       "       1, 1, 0, 1, 1, 1, 1, 0, 1, 1, 1, 1, 0, 1, 0, 1, 1, 1, 0, 1, 0, 1,\n",
       "       1, 1, 1], dtype=int64)"
      ]
     },
     "execution_count": 207,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "pred_best_random =  gs.predict(X_test)\n",
    "pred_best_random"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 208,
   "metadata": {},
   "outputs": [],
   "source": [
    "### Get a confusion matrix and then get sensitivity by hand\n",
    "con_mat = confusion_matrix(y_test, pred_best_random)\n",
    "#sensitivity <- TP / (TP + FN)\n",
    "#specificity <- TN / (TN + FP)\n",
    "con_mat\n",
    "#tn, fp, fn, tp\n",
    "sensitivity =  con_mat[1][1] / (con_mat[1][1] + con_mat[1][0])\n",
    "specificity = con_mat[0][0] / (con_mat[0][0] + con_mat[0][1])\n",
    "### \n",
    "balanced_accuracy =  (sensitivity+specificity)/2"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 230,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>sensitivity</th>\n",
       "      <th>specificity</th>\n",
       "      <th>balanced_accuracy</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0.931373</td>\n",
       "      <td>0.591304</td>\n",
       "      <td>0.761338</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   sensitivity  specificity  balanced_accuracy\n",
       "0     0.931373     0.591304           0.761338"
      ]
     },
     "execution_count": 230,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "### Fix this later\n",
    "##\n",
    "gs.best_score_\n",
    "sensitivity = pd.DataFrame(sensitivity, columns = [\"sensitivity\"], index = [0])\n",
    "specificity = pd.DataFrame(specificity, columns = [\"specificity\"], index = [0])\n",
    "balanced_accuracy = pd.DataFrame(balanced_accuracy, columns = [\"balanced_accuracy\"], index = [0])\n",
    "frames = [sensitivity, specificity, balanced_accuracy]\n",
    "metrics_dat = pd.concat(frames, axis = 1)\n",
    "metrics_dat"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 221,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([0.99669075, 0.3039529 , 0.96171224, 0.9825471 , 0.1756365 ,\n",
       "       0.96707445, 0.9858467 , 0.9787187 , 0.09378103, 0.9600673 ,\n",
       "       0.6532217 , 0.8880726 , 0.9934697 , 0.87080246, 0.92724866,\n",
       "       0.89548165, 0.15171881, 0.06846377, 0.8311499 , 0.9900345 ,\n",
       "       0.04836588, 0.15171881, 0.98247564, 0.9875081 , 0.97913927,\n",
       "       0.92019904, 0.05885879, 0.99715304, 0.9702219 , 0.9242021 ,\n",
       "       0.88551176, 0.98533463, 0.9688826 , 0.79240763, 0.05203338,\n",
       "       0.9898415 , 0.08801579, 0.75368285, 0.98339707, 0.59956837,\n",
       "       0.971642  , 0.06794025, 0.94708323, 0.5736642 , 0.9567686 ,\n",
       "       0.8592954 , 0.97044814, 0.5447576 , 0.27098116, 0.9658098 ,\n",
       "       0.9899087 , 0.96157974, 0.55741775, 0.50520504, 0.96337575,\n",
       "       0.9122038 , 0.49444118, 0.54873765, 0.04847186, 0.96531373,\n",
       "       0.9711691 , 0.995182  , 0.04498342, 0.98862225, 0.87195045,\n",
       "       0.9578738 , 0.8980047 , 0.997152  , 0.8923392 , 0.9879451 ,\n",
       "       0.9693034 , 0.9891667 , 0.7545509 , 0.24176319, 0.9910943 ,\n",
       "       0.48782963, 0.97697985, 0.9567686 , 0.9966863 , 0.92748106,\n",
       "       0.32479283, 0.97331154, 0.13426243, 0.9721963 , 0.50715417,\n",
       "       0.5797605 , 0.49117747, 0.98876977, 0.9668245 , 0.8948903 ,\n",
       "       0.9972383 , 0.9926036 , 0.97273684, 0.23097932, 0.992761  ,\n",
       "       0.01422725, 0.92234683, 0.8994015 , 0.983344  , 0.9936504 ,\n",
       "       0.9568299 , 0.99734455, 0.9869698 , 0.96776116, 0.98372847,\n",
       "       0.40567455, 0.12175842, 0.64661115, 0.41163364, 0.97944427,\n",
       "       0.9420622 , 0.9567686 , 0.9827943 , 0.9966234 , 0.9919137 ,\n",
       "       0.98138714, 0.7373411 , 0.76631176, 0.895309  , 0.40967575,\n",
       "       0.8733523 , 0.21455088, 0.63937527, 0.964639  , 0.88987285,\n",
       "       0.9617861 , 0.7968021 , 0.9832392 , 0.9150652 , 0.98483306,\n",
       "       0.98197997, 0.85848546, 0.99146265, 0.9004553 , 0.7420014 ,\n",
       "       0.5006449 , 0.8600675 , 0.9528046 , 0.08579257, 0.9693034 ,\n",
       "       0.36971125, 0.998669  , 0.960335  , 0.98250383, 0.09734955,\n",
       "       0.44409466, 0.98861456, 0.9220303 , 0.9677563 , 0.17822368,\n",
       "       0.99360836, 0.41617036, 0.9947785 , 0.9492622 , 0.93109685,\n",
       "       0.98593706, 0.93404835, 0.08696041, 0.99813795, 0.77662337,\n",
       "       0.99641526, 0.98608273, 0.74695265, 0.9970739 , 0.9796674 ,\n",
       "       0.84666365, 0.9357123 , 0.05720388, 0.42928883, 0.9893764 ,\n",
       "       0.6429541 , 0.04949354, 0.97525704, 0.83909905, 0.9908178 ,\n",
       "       0.7887417 , 0.64206946, 0.50362617, 0.18024161, 0.90526646,\n",
       "       0.97470266, 0.92289764, 0.25260478, 0.12175842, 0.9830498 ,\n",
       "       0.967145  , 0.9892426 , 0.99699795, 0.99769634, 0.967721  ,\n",
       "       0.9851517 , 0.29438433, 0.599082  , 0.98948914, 0.9766346 ,\n",
       "       0.96967375, 0.90814745, 0.9553925 , 0.99401456, 0.9978801 ,\n",
       "       0.11541988, 0.99450535, 0.09739505, 0.8013599 , 0.95849836,\n",
       "       0.06088112, 0.9023771 , 0.04690027, 0.9889521 , 0.4182704 ,\n",
       "       0.8854954 , 0.9929161 , 0.964273  , 0.90430784, 0.9939732 ,\n",
       "       0.92486674, 0.98191446, 0.3339379 , 0.9942193 , 0.95415235,\n",
       "       0.8811056 , 0.05244545, 0.50715417, 0.87946093, 0.87940913,\n",
       "       0.4050173 , 0.98593706, 0.7104462 , 0.9570275 , 0.11510473,\n",
       "       0.93902224, 0.99761593, 0.97727066, 0.9717846 , 0.6970007 ,\n",
       "       0.06744374, 0.52172416, 0.71168375, 0.8527077 , 0.77520967,\n",
       "       0.1756162 , 0.9387168 , 0.98964745, 0.94856626, 0.33568868,\n",
       "       0.74513125, 0.99509615, 0.7895346 , 0.98876977, 0.7785938 ,\n",
       "       0.47310337, 0.960474  , 0.9898164 , 0.99376196, 0.36368003,\n",
       "       0.9950912 , 0.9919137 , 0.22813882, 0.08701038, 0.9409798 ,\n",
       "       0.85499984, 0.9770935 , 0.92724866, 0.71011484, 0.9925276 ,\n",
       "       0.94146043, 0.73066396, 0.8598251 , 0.9939732 , 0.6095575 ,\n",
       "       0.98979294, 0.9779089 , 0.04999681, 0.9778291 , 0.9877843 ,\n",
       "       0.68965155, 0.96389186, 0.38699383, 0.08074366, 0.96913165,\n",
       "       0.9706771 , 0.9725866 , 0.05203338, 0.8547624 , 0.9579807 ,\n",
       "       0.99441034, 0.98997074, 0.9085098 , 0.9393566 , 0.9286781 ,\n",
       "       0.04498342, 0.9158879 , 0.99210113, 0.99401337, 0.98887604,\n",
       "       0.9155596 , 0.9887637 , 0.5627102 , 0.0715299 , 0.90648025,\n",
       "       0.9455009 , 0.95884794, 0.18327688, 0.91873133, 0.39650446,\n",
       "       0.59111637, 0.98037153, 0.9771528 , 0.54020876, 0.9974076 ,\n",
       "       0.9914675 , 0.9975568 , 0.9873467 , 0.89500827, 0.9289324 ,\n",
       "       0.9852697 , 0.97244227, 0.98798996, 0.96429837, 0.9087111 ,\n",
       "       0.9664188 , 0.04774589, 0.98790914, 0.78205186, 0.9919137 ,\n",
       "       0.99528223, 0.9755671 , 0.9688514 , 0.77419835, 0.19017705,\n",
       "       0.90648025, 0.9895381 , 0.9403721 , 0.06673332, 0.9921847 ,\n",
       "       0.982938  , 0.97934395, 0.08085779, 0.8840916 , 0.97934395,\n",
       "       0.9663771 , 0.79362947, 0.98756313, 0.9879451 , 0.95972204,\n",
       "       0.02293392, 0.94146043, 0.99389935, 0.9883995 , 0.98789036,\n",
       "       0.44342136, 0.99316686, 0.87300044, 0.9821447 , 0.92703354,\n",
       "       0.2851194 , 0.7840823 , 0.9569492 , 0.9908178 , 0.9638751 ,\n",
       "       0.98789036, 0.64857197, 0.04562635, 0.98935306, 0.19445448,\n",
       "       0.93779474, 0.9166294 , 0.44000992, 0.96707445, 0.37125695,\n",
       "       0.9697056 , 0.8923392 , 0.9555931 , 0.45184252, 0.05831425,\n",
       "       0.98944384, 0.9916375 , 0.8946682 , 0.09734955, 0.9616025 ,\n",
       "       0.09739505, 0.356245  , 0.90385276, 0.9978801 , 0.08610877,\n",
       "       0.95520693, 0.96739966, 0.9939732 , 0.04836588, 0.98347074,\n",
       "       0.9702093 , 0.94146043, 0.6553898 , 0.850993  , 0.84585476,\n",
       "       0.97697985, 0.9603592 , 0.810053  , 0.40777236, 0.9919727 ,\n",
       "       0.9668245 , 0.9925264 , 0.9594079 , 0.03341477, 0.9810326 ,\n",
       "       0.97826606, 0.990635  , 0.9787187 , 0.04389984, 0.99128234,\n",
       "       0.3706376 , 0.55634093, 0.98677295, 0.7584565 , 0.04999681,\n",
       "       0.99541414, 0.09041525, 0.6512698 , 0.9504252 , 0.88008344,\n",
       "       0.9762255 ], dtype=float32)"
      ]
     },
     "execution_count": 221,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "### Need to get the predicted probabilities\n",
    "pred_prob =  gs.predict_proba(X_test)\n",
    "pred_prob[:,1]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 227,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "RandomizedSearchCV(cv=10, estimator=XGBClassifier(missing=nan), n_jobs=-1,\n",
       "                   param_distributions={'alpha': [0, 0.1, 0.2, 0.3],\n",
       "                                        'colsample_bytree': [0.5, 0.6, 0.7],\n",
       "                                        'eta': [0.01, 0.001, 0.3],\n",
       "                                        'lambda': [1, 1.1, 1.2, 1.3, 1.4],\n",
       "                                        'max_depth': [6, 12, 15, 18],\n",
       "                                        'scale_pos_weight ': [0.25, 0.5, 1],\n",
       "                                        'subsample': [0.5, 0.6, 0.7]})"
      ]
     },
     "execution_count": 227,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "### Now save the model\n",
    "import pickle\n",
    "import joblib\n",
    "filename = \"gs.sav\"\n",
    "joblib.dump(gs, filename)\n",
    "load_model = joblib.load(filename)\n",
    "load_model"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": []
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.7.7"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 4
}
