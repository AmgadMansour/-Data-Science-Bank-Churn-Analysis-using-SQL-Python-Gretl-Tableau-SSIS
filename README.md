# -Data-Science-Bank-Churn-Modeling

A bank operating over three countries in Europe has encountered a problem where a lot of customers have been leaving the bank lately. The bank took a random sample of 10,000 customers to represent the population of its customers and kept tracking of who is leaving the bank over six months and gave you the attached data to perform data mining and deliver some insights that can help the bank solve the problem.
The data given to you for each customer is his customer id, surname, credit score, country, gender, age, tenure (amount of years with the bank), balance, number of products with the bank (credit card, debit card, loan, etc.), whether he has credit card or not, activity status, estimated salary and whether he left the bank or not.

Attributes:

1- CustomerId
2- Surname
3- CreditScore
4- Geography
5- Gender
6- Age
7- Tenure
8- Balance
9- NumOfProducts
10- HasCrCard
11- IsActiveMember
12- EstimatedSalary
13- Exited


{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Geodemographic Segmentation Model\n",
    "\n",
    "Dataset: https://www.superdatascience.com/training/\n",
    "\n",
    ": "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "/usr/local/lib/python2.7/site-packages/matplotlib/font_manager.py:273: UserWarning: Matplotlib is building the font cache using fc-list. This may take a moment.\n",
      "  warnings.warn('Matplotlib is building the font cache using fc-list. This may take a moment.')\n"
     ]
    }
   ],
   "source": [
    "%matplotlib inline \n",
    "import warnings\n",
    "warnings.filterwarnings(\"ignore\")\n",
    "from __future__ import division\n",
    "import pandas\n",
    "import csv\n",
    "import numpy as np\n",
    "import statsmodels.api as sm\n",
    "import statsmodels.discrete.discrete_model as smdis\n",
    "import statsmodels.stats.outliers_influence as outliers\n",
    "import matplotlib.pyplot as plt"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>RowNumber</th>\n",
       "      <th>CustomerId</th>\n",
       "      <th>Surname</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Geography</th>\n",
       "      <th>Gender</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>HasCrCard</th>\n",
       "      <th>IsActiveMember</th>\n",
       "      <th>EstimatedSalary</th>\n",
       "      <th>Exited</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>15634602</td>\n",
       "      <td>Hargrave</td>\n",
       "      <td>619</td>\n",
       "      <td>France</td>\n",
       "      <td>Female</td>\n",
       "      <td>42</td>\n",
       "      <td>2</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>101348.88</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2</td>\n",
       "      <td>15647311</td>\n",
       "      <td>Hill</td>\n",
       "      <td>608</td>\n",
       "      <td>Spain</td>\n",
       "      <td>Female</td>\n",
       "      <td>41</td>\n",
       "      <td>1</td>\n",
       "      <td>83807.86</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>112542.58</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>3</td>\n",
       "      <td>15619304</td>\n",
       "      <td>Onio</td>\n",
       "      <td>502</td>\n",
       "      <td>France</td>\n",
       "      <td>Female</td>\n",
       "      <td>42</td>\n",
       "      <td>8</td>\n",
       "      <td>159660.80</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>113931.57</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>4</td>\n",
       "      <td>15701354</td>\n",
       "      <td>Boni</td>\n",
       "      <td>699</td>\n",
       "      <td>France</td>\n",
       "      <td>Female</td>\n",
       "      <td>39</td>\n",
       "      <td>1</td>\n",
       "      <td>0.00</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>93826.63</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>5</td>\n",
       "      <td>15737888</td>\n",
       "      <td>Mitchell</td>\n",
       "      <td>850</td>\n",
       "      <td>Spain</td>\n",
       "      <td>Female</td>\n",
       "      <td>43</td>\n",
       "      <td>2</td>\n",
       "      <td>125510.82</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>79084.10</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   RowNumber  CustomerId   Surname  CreditScore Geography  Gender  Age  \\\n",
       "0          1    15634602  Hargrave          619    France  Female   42   \n",
       "1          2    15647311      Hill          608     Spain  Female   41   \n",
       "2          3    15619304      Onio          502    France  Female   42   \n",
       "3          4    15701354      Boni          699    France  Female   39   \n",
       "4          5    15737888  Mitchell          850     Spain  Female   43   \n",
       "\n",
       "   Tenure    Balance  NumOfProducts  HasCrCard  IsActiveMember  \\\n",
       "0       2       0.00              1          1               1   \n",
       "1       1   83807.86              1          0               1   \n",
       "2       8  159660.80              3          1               0   \n",
       "3       1       0.00              2          0               0   \n",
       "4       2  125510.82              1          1               1   \n",
       "\n",
       "   EstimatedSalary  Exited  \n",
       "0        101348.88       1  \n",
       "1        112542.58       0  \n",
       "2        113931.57       1  \n",
       "3         93826.63       0  \n",
       "4         79084.10       0  "
      ]
     },
     "execution_count": 2,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df = pandas.DataFrame.from_csv('Churn-Modelling.csv', index_col=None)\n",
    "df[:5]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#### Variables\n",
    "\n",
    "- Exited: dependent variable (y) -->  is binary\n",
    "- Gender, Geography: categorical independent variable (x)\n",
    "- All the remaining variables: numeric independent variable (x)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "df_y = df['Exited']\n",
    "df_x = df.drop(['Exited'], axis = 1)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Create dummy variables for the categorical variables:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>France</th>\n",
       "      <th>Germany</th>\n",
       "      <th>Spain</th>\n",
       "      <th>Female</th>\n",
       "      <th>Male</th>\n",
       "      <th>RowNumber</th>\n",
       "      <th>CustomerId</th>\n",
       "      <th>Surname</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Geography</th>\n",
       "      <th>Gender</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>HasCrCard</th>\n",
       "      <th>IsActiveMember</th>\n",
       "      <th>EstimatedSalary</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>15634602</td>\n",
       "      <td>Hargrave</td>\n",
       "      <td>619</td>\n",
       "      <td>France</td>\n",
       "      <td>Female</td>\n",
       "      <td>42</td>\n",
       "      <td>2</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>101348.88</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>2</td>\n",
       "      <td>15647311</td>\n",
       "      <td>Hill</td>\n",
       "      <td>608</td>\n",
       "      <td>Spain</td>\n",
       "      <td>Female</td>\n",
       "      <td>41</td>\n",
       "      <td>1</td>\n",
       "      <td>83807.86</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>112542.58</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>3</td>\n",
       "      <td>15619304</td>\n",
       "      <td>Onio</td>\n",
       "      <td>502</td>\n",
       "      <td>France</td>\n",
       "      <td>Female</td>\n",
       "      <td>42</td>\n",
       "      <td>8</td>\n",
       "      <td>159660.80</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>113931.57</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>4</td>\n",
       "      <td>15701354</td>\n",
       "      <td>Boni</td>\n",
       "      <td>699</td>\n",
       "      <td>France</td>\n",
       "      <td>Female</td>\n",
       "      <td>39</td>\n",
       "      <td>1</td>\n",
       "      <td>0.00</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>93826.63</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>5</td>\n",
       "      <td>15737888</td>\n",
       "      <td>Mitchell</td>\n",
       "      <td>850</td>\n",
       "      <td>Spain</td>\n",
       "      <td>Female</td>\n",
       "      <td>43</td>\n",
       "      <td>2</td>\n",
       "      <td>125510.82</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>79084.10</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   France  Germany  Spain  Female  Male  RowNumber  CustomerId   Surname  \\\n",
       "0       1        0      0       1     0          1    15634602  Hargrave   \n",
       "1       0        0      1       1     0          2    15647311      Hill   \n",
       "2       1        0      0       1     0          3    15619304      Onio   \n",
       "3       1        0      0       1     0          4    15701354      Boni   \n",
       "4       0        0      1       1     0          5    15737888  Mitchell   \n",
       "\n",
       "   CreditScore Geography  Gender  Age  Tenure    Balance  NumOfProducts  \\\n",
       "0          619    France  Female   42       2       0.00              1   \n",
       "1          608     Spain  Female   41       1   83807.86              1   \n",
       "2          502    France  Female   42       8  159660.80              3   \n",
       "3          699    France  Female   39       1       0.00              2   \n",
       "4          850     Spain  Female   43       2  125510.82              1   \n",
       "\n",
       "   HasCrCard  IsActiveMember  EstimatedSalary  \n",
       "0          1               1        101348.88  \n",
       "1          0               1        112542.58  \n",
       "2          1               0        113931.57  \n",
       "3          0               0         93826.63  \n",
       "4          1               1         79084.10  "
      ]
     },
     "execution_count": 4,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "dummy = pandas.get_dummies(df_x['Gender'])\n",
    "df_x = dummy.join(df_x)\n",
    "dummy = pandas.get_dummies(df_x['Geography'])\n",
    "df_x = dummy.join(df_x)\n",
    "df_x[:5]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Only one dummy variable should be used to avoid the \"dummy variable trap\":"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Germany</th>\n",
       "      <th>Spain</th>\n",
       "      <th>Female</th>\n",
       "      <th>RowNumber</th>\n",
       "      <th>CustomerId</th>\n",
       "      <th>Surname</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>HasCrCard</th>\n",
       "      <th>IsActiveMember</th>\n",
       "      <th>EstimatedSalary</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>15634602</td>\n",
       "      <td>Hargrave</td>\n",
       "      <td>619</td>\n",
       "      <td>42</td>\n",
       "      <td>2</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>101348.88</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>2</td>\n",
       "      <td>15647311</td>\n",
       "      <td>Hill</td>\n",
       "      <td>608</td>\n",
       "      <td>41</td>\n",
       "      <td>1</td>\n",
       "      <td>83807.86</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>112542.58</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>15619304</td>\n",
       "      <td>Onio</td>\n",
       "      <td>502</td>\n",
       "      <td>42</td>\n",
       "      <td>8</td>\n",
       "      <td>159660.80</td>\n",
       "      <td>3</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>113931.57</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>4</td>\n",
       "      <td>15701354</td>\n",
       "      <td>Boni</td>\n",
       "      <td>699</td>\n",
       "      <td>39</td>\n",
       "      <td>1</td>\n",
       "      <td>0.00</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>93826.63</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>5</td>\n",
       "      <td>15737888</td>\n",
       "      <td>Mitchell</td>\n",
       "      <td>850</td>\n",
       "      <td>43</td>\n",
       "      <td>2</td>\n",
       "      <td>125510.82</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>79084.10</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   Germany  Spain  Female  RowNumber  CustomerId   Surname  CreditScore  Age  \\\n",
       "0        0      0       1          1    15634602  Hargrave          619   42   \n",
       "1        0      1       1          2    15647311      Hill          608   41   \n",
       "2        0      0       1          3    15619304      Onio          502   42   \n",
       "3        0      0       1          4    15701354      Boni          699   39   \n",
       "4        0      1       1          5    15737888  Mitchell          850   43   \n",
       "\n",
       "   Tenure    Balance  NumOfProducts  HasCrCard  IsActiveMember  \\\n",
       "0       2       0.00              1          1               1   \n",
       "1       1   83807.86              1          0               1   \n",
       "2       8  159660.80              3          1               0   \n",
       "3       1       0.00              2          0               0   \n",
       "4       2  125510.82              1          1               1   \n",
       "\n",
       "   EstimatedSalary  \n",
       "0        101348.88  \n",
       "1        112542.58  \n",
       "2        113931.57  \n",
       "3         93826.63  \n",
       "4         79084.10  "
      ]
     },
     "execution_count": 5,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_x = df_x.drop(['Gender', 'Male', 'France', 'Geography'], axis =1)\n",
    "df_x[:5]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Add a constant \\begin{align} b_0 \\end{align} to the model:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>const</th>\n",
       "      <th>Germany</th>\n",
       "      <th>Spain</th>\n",
       "      <th>Female</th>\n",
       "      <th>RowNumber</th>\n",
       "      <th>CustomerId</th>\n",
       "      <th>Surname</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>HasCrCard</th>\n",
       "      <th>IsActiveMember</th>\n",
       "      <th>EstimatedSalary</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>15634602</td>\n",
       "      <td>Hargrave</td>\n",
       "      <td>619</td>\n",
       "      <td>42</td>\n",
       "      <td>2</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>101348.88</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>2</td>\n",
       "      <td>15647311</td>\n",
       "      <td>Hill</td>\n",
       "      <td>608</td>\n",
       "      <td>41</td>\n",
       "      <td>1</td>\n",
       "      <td>83807.86</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>112542.58</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   const  Germany  Spain  Female  RowNumber  CustomerId   Surname  \\\n",
       "0      1        0      0       1          1    15634602  Hargrave   \n",
       "1      1        0      1       1          2    15647311      Hill   \n",
       "\n",
       "   CreditScore  Age  Tenure   Balance  NumOfProducts  HasCrCard  \\\n",
       "0          619   42       2      0.00              1          1   \n",
       "1          608   41       1  83807.86              1          0   \n",
       "\n",
       "   IsActiveMember  EstimatedSalary  \n",
       "0               1        101348.88  \n",
       "1               1        112542.58  "
      ]
     },
     "execution_count": 6,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_x = sm.add_constant(df_x)\n",
    "df_x[:2]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Exclude the variables that should not affect the model:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>const</th>\n",
       "      <th>Germany</th>\n",
       "      <th>Spain</th>\n",
       "      <th>Female</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>HasCrCard</th>\n",
       "      <th>IsActiveMember</th>\n",
       "      <th>EstimatedSalary</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>619</td>\n",
       "      <td>42</td>\n",
       "      <td>2</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>101348.88</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>608</td>\n",
       "      <td>41</td>\n",
       "      <td>1</td>\n",
       "      <td>83807.86</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>112542.58</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   const  Germany  Spain  Female  CreditScore  Age  Tenure   Balance  \\\n",
       "0      1        0      0       1          619   42       2      0.00   \n",
       "1      1        0      1       1          608   41       1  83807.86   \n",
       "\n",
       "   NumOfProducts  HasCrCard  IsActiveMember  EstimatedSalary  \n",
       "0              1          1               1        101348.88  \n",
       "1              1          0               1        112542.58  "
      ]
     },
     "execution_count": 7,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_x = df_x.drop(['RowNumber', 'CustomerId', 'Surname'], axis = 1)\n",
    "df_x[:2]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Create a model with all the remaining variables:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 8,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.428068\n",
      "         Iterations 6\n",
      "                           Logit Regression Results                           \n",
      "==============================================================================\n",
      "Dep. Variable:                 Exited   No. Observations:                10000\n",
      "Model:                          Logit   Df Residuals:                     9988\n",
      "Method:                           MLE   Df Model:                           11\n",
      "Date:                Wed, 28 Dec 2016   Pseudo R-squ.:                  0.1532\n",
      "Time:                        13:04:11   Log-Likelihood:                -4280.7\n",
      "converged:                       True   LL-Null:                       -5054.9\n",
      "                                        LLR p-value:                     0.000\n",
      "===================================================================================\n",
      "                      coef    std err          z      P>|z|      [95.0% Conf. Int.]\n",
      "-----------------------------------------------------------------------------------\n",
      "const              -3.9208      0.245    -15.980      0.000        -4.402    -3.440\n",
      "Germany             0.7747      0.068     11.448      0.000         0.642     0.907\n",
      "Spain               0.0352      0.071      0.499      0.618        -0.103     0.174\n",
      "Female              0.5285      0.054      9.699      0.000         0.422     0.635\n",
      "CreditScore        -0.0007      0.000     -2.384      0.017        -0.001    -0.000\n",
      "Age                 0.0727      0.003     28.230      0.000         0.068     0.078\n",
      "Tenure             -0.0159      0.009     -1.705      0.088        -0.034     0.002\n",
      "Balance          2.637e-06   5.14e-07      5.128      0.000      1.63e-06  3.64e-06\n",
      "NumOfProducts      -0.1015      0.047     -2.154      0.031        -0.194    -0.009\n",
      "HasCrCard          -0.0447      0.059     -0.753      0.452        -0.161     0.072\n",
      "IsActiveMember     -1.0754      0.058    -18.643      0.000        -1.189    -0.962\n",
      "EstimatedSalary  4.807e-07   4.74e-07      1.015      0.310     -4.48e-07  1.41e-06\n",
      "===================================================================================\n",
      "\n",
      "_____P-values____\n",
      "const               1.759879e-57\n",
      "Germany             2.413604e-30\n",
      "Spain               6.180842e-01\n",
      "Female              3.044696e-22\n",
      "CreditScore         1.712765e-02\n",
      "Age                2.521111e-175\n",
      "Tenure              8.821318e-02\n",
      "Balance             2.922724e-07\n",
      "NumOfProducts       3.124760e-02\n",
      "HasCrCard           4.515135e-01\n",
      "IsActiveMember      1.434412e-77\n",
      "EstimatedSalary     3.101752e-01\n",
      "dtype: float64\n",
      "\n",
      "_____Highest p-value____\n",
      "\n",
      " Spain    0.618084\n",
      "dtype: float64 \n",
      "\n",
      "\n",
      "____Confusion Matrix___\n",
      "[[ 7666.   297.]\n",
      " [ 1600.   437.]]\n",
      "\n",
      "Number of cases correctly predicted: 8103.0 (81.03 %)\n"
     ]
    }
   ],
   "source": [
    "model1 = smdis.Logit(df_y, df_x).fit()\n",
    "print model1.summary()\n",
    "print \"\\n_____P-values____\"\n",
    "p = model1.pvalues\n",
    "print p\n",
    "print \"\\n_____Highest p-value____\"\n",
    "print \"\\n %s \\n\" % p[p ==max(p)]\n",
    "print \"\\n____Confusion Matrix___\"\n",
    "cm = model1.pred_table()\n",
    "print cm\n",
    "print \"\\nNumber of cases correctly predicted: %s (%s %%)\" % (cm[0][0] + cm[1][1], (cm[0][0] + cm[1][1])*100/np.sum(cm))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Note: \n",
    "\n",
    "__\"Pseudo R-squ.\"__ -  pseudo R-squared. \n",
    "Logistic regression does not have an equivalent to the R-squared that is found in OLS regression; however, many people have tried to come up with one.  There are a wide variety of pseudo-R-square statistics.  Because this statistic does not mean what R-square means in OLS regression (the proportion of variance explained by the predictors), we suggest interpreting this statistic with great caution.        (source: http://www.ats.ucla.edu/stat/stata/output/stata_logistic.htm) \n",
    "\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "__Backward Elimination__"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "New model without the variable with the highest p-value: \"Spain\""
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 9,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>const</th>\n",
       "      <th>Germany</th>\n",
       "      <th>Female</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>HasCrCard</th>\n",
       "      <th>IsActiveMember</th>\n",
       "      <th>EstimatedSalary</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>619</td>\n",
       "      <td>42</td>\n",
       "      <td>2</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>101348.88</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>608</td>\n",
       "      <td>41</td>\n",
       "      <td>1</td>\n",
       "      <td>83807.86</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>112542.58</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   const  Germany  Female  CreditScore  Age  Tenure   Balance  NumOfProducts  \\\n",
       "0      1        0       1          619   42       2      0.00              1   \n",
       "1      1        0       1          608   41       1  83807.86              1   \n",
       "\n",
       "   HasCrCard  IsActiveMember  EstimatedSalary  \n",
       "0          1               1        101348.88  \n",
       "1          0               1        112542.58  "
      ]
     },
     "execution_count": 9,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_x1 = df_x.drop(['Spain'], axis = 1)\n",
    "df_x1[:2]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.428080\n",
      "         Iterations 6\n",
      "                           Logit Regression Results                           \n",
      "==============================================================================\n",
      "Dep. Variable:                 Exited   No. Observations:                10000\n",
      "Model:                          Logit   Df Residuals:                     9989\n",
      "Method:                           MLE   Df Model:                           10\n",
      "Date:                Wed, 28 Dec 2016   Pseudo R-squ.:                  0.1531\n",
      "Time:                        13:04:12   Log-Likelihood:                -4280.8\n",
      "converged:                       True   LL-Null:                       -5054.9\n",
      "                                        LLR p-value:                     0.000\n",
      "===================================================================================\n",
      "                      coef    std err          z      P>|z|      [95.0% Conf. Int.]\n",
      "-----------------------------------------------------------------------------------\n",
      "const              -3.9110      0.245    -15.994      0.000        -4.390    -3.432\n",
      "Germany             0.7629      0.063     12.041      0.000         0.639     0.887\n",
      "Female              0.5283      0.054      9.697      0.000         0.422     0.635\n",
      "CreditScore        -0.0007      0.000     -2.378      0.017        -0.001    -0.000\n",
      "Age                 0.0727      0.003     28.238      0.000         0.068     0.078\n",
      "Tenure             -0.0160      0.009     -1.708      0.088        -0.034     0.002\n",
      "Balance          2.637e-06   5.14e-07      5.129      0.000      1.63e-06  3.65e-06\n",
      "NumOfProducts      -0.1013      0.047     -2.149      0.032        -0.194    -0.009\n",
      "HasCrCard          -0.0449      0.059     -0.757      0.449        -0.161     0.071\n",
      "IsActiveMember     -1.0752      0.058    -18.640      0.000        -1.188    -0.962\n",
      "EstimatedSalary  4.813e-07   4.74e-07      1.016      0.310     -4.47e-07  1.41e-06\n",
      "===================================================================================\n",
      "\n",
      "_____P-values____\n",
      "const               1.405607e-57\n",
      "Germany             2.162265e-33\n",
      "Female              3.114487e-22\n",
      "CreditScore         1.739401e-02\n",
      "Age                1.996715e-175\n",
      "Tenure              8.764548e-02\n",
      "Balance             2.913057e-07\n",
      "NumOfProducts       3.161546e-02\n",
      "HasCrCard           4.489327e-01\n",
      "IsActiveMember      1.531167e-77\n",
      "EstimatedSalary     3.095145e-01\n",
      "dtype: float64\n",
      "\n",
      "_____Highest p-value____\n",
      "\n",
      " HasCrCard    0.448933\n",
      "dtype: float64 \n",
      "\n",
      "\n",
      "____Confusion Matrix___\n",
      "[[ 7665.   298.]\n",
      " [ 1602.   435.]]\n",
      "\n",
      "Number of cases correctly predicted: 8100.0 (81.0 %)\n"
     ]
    }
   ],
   "source": [
    "model2 = smdis.Logit(df_y, df_x1).fit()\n",
    "print model2.summary()\n",
    "print \"\\n_____P-values____\"\n",
    "p = model2.pvalues\n",
    "print p\n",
    "print \"\\n_____Highest p-value____\"\n",
    "print \"\\n %s \\n\" % p[p ==max(p)]\n",
    "print \"\\n____Confusion Matrix___\"\n",
    "cm = model2.pred_table()\n",
    "print cm\n",
    "print \"\\nNumber of cases correctly predicted: %s (%s %%)\" % (cm[0][0] + cm[1][1], (cm[0][0] + cm[1][1])*100/np.sum(cm))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "New model without the variable with the highest p-value: \"HasCrCard\""
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>const</th>\n",
       "      <th>Germany</th>\n",
       "      <th>Female</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>IsActiveMember</th>\n",
       "      <th>EstimatedSalary</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>619</td>\n",
       "      <td>42</td>\n",
       "      <td>2</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>101348.88</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>608</td>\n",
       "      <td>41</td>\n",
       "      <td>1</td>\n",
       "      <td>83807.86</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>112542.58</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   const  Germany  Female  CreditScore  Age  Tenure   Balance  NumOfProducts  \\\n",
       "0      1        0       1          619   42       2      0.00              1   \n",
       "1      1        0       1          608   41       1  83807.86              1   \n",
       "\n",
       "   IsActiveMember  EstimatedSalary  \n",
       "0               1        101348.88  \n",
       "1               1        112542.58  "
      ]
     },
     "execution_count": 11,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_x2 = df_x1.drop(['HasCrCard'], axis = 1)\n",
    "df_x2[:2]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.428109\n",
      "         Iterations 6\n",
      "                           Logit Regression Results                           \n",
      "==============================================================================\n",
      "Dep. Variable:                 Exited   No. Observations:                10000\n",
      "Model:                          Logit   Df Residuals:                     9990\n",
      "Method:                           MLE   Df Model:                            9\n",
      "Date:                Wed, 28 Dec 2016   Pseudo R-squ.:                  0.1531\n",
      "Time:                        13:04:12   Log-Likelihood:                -4281.1\n",
      "converged:                       True   LL-Null:                       -5054.9\n",
      "                                        LLR p-value:                     0.000\n",
      "===================================================================================\n",
      "                      coef    std err          z      P>|z|      [95.0% Conf. Int.]\n",
      "-----------------------------------------------------------------------------------\n",
      "const              -3.9444      0.241    -16.395      0.000        -4.416    -3.473\n",
      "Germany             0.7619      0.063     12.028      0.000         0.638     0.886\n",
      "Female              0.5285      0.054      9.700      0.000         0.422     0.635\n",
      "CreditScore        -0.0007      0.000     -2.369      0.018        -0.001    -0.000\n",
      "Age                 0.0727      0.003     28.243      0.000         0.068     0.078\n",
      "Tenure             -0.0162      0.009     -1.727      0.084        -0.034     0.002\n",
      "Balance          2.645e-06   5.14e-07      5.146      0.000      1.64e-06  3.65e-06\n",
      "NumOfProducts      -0.1013      0.047     -2.150      0.032        -0.194    -0.009\n",
      "IsActiveMember     -1.0744      0.058    -18.631      0.000        -1.187    -0.961\n",
      "EstimatedSalary  4.818e-07   4.74e-07      1.017      0.309     -4.47e-07  1.41e-06\n",
      "===================================================================================\n",
      "\n",
      "_____P-values____\n",
      "const               2.068294e-60\n",
      "Germany             2.545658e-33\n",
      "Female              3.024897e-22\n",
      "CreditScore         1.782382e-02\n",
      "Age                1.731605e-175\n",
      "Tenure              8.415226e-02\n",
      "Balance             2.660186e-07\n",
      "NumOfProducts       3.152337e-02\n",
      "IsActiveMember      1.809198e-77\n",
      "EstimatedSalary     3.090842e-01\n",
      "dtype: float64\n",
      "\n",
      "_____Highest p-value____\n",
      "\n",
      " EstimatedSalary    0.309084\n",
      "dtype: float64 \n",
      "\n",
      "\n",
      "____Confusion Matrix___\n",
      "[[ 7673.   290.]\n",
      " [ 1599.   438.]]\n",
      "\n",
      "Number of cases correctly predicted: 8111.0 (81.11 %)\n"
     ]
    }
   ],
   "source": [
    "model3 = smdis.Logit(df_y, df_x2).fit()\n",
    "print model3.summary()\n",
    "print \"\\n_____P-values____\"\n",
    "p = model3.pvalues\n",
    "print p\n",
    "print \"\\n_____Highest p-value____\"\n",
    "print \"\\n %s \\n\" % p[p ==max(p)]\n",
    "print \"\\n____Confusion Matrix___\"\n",
    "cm = model3.pred_table()\n",
    "print cm\n",
    "print \"\\nNumber of cases correctly predicted: %s (%s %%)\" % (cm[0][0] + cm[1][1], (cm[0][0] + cm[1][1])*100/np.sum(cm))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "New model without the variable with the highest p-value: \"EstimatedSalary\""
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>const</th>\n",
       "      <th>Germany</th>\n",
       "      <th>Female</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>IsActiveMember</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>619</td>\n",
       "      <td>42</td>\n",
       "      <td>2</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>608</td>\n",
       "      <td>41</td>\n",
       "      <td>1</td>\n",
       "      <td>83807.86</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   const  Germany  Female  CreditScore  Age  Tenure   Balance  NumOfProducts  \\\n",
       "0      1        0       1          619   42       2      0.00              1   \n",
       "1      1        0       1          608   41       1  83807.86              1   \n",
       "\n",
       "   IsActiveMember  \n",
       "0               1  \n",
       "1               1  "
      ]
     },
     "execution_count": 13,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_x3 = df_x2.drop(['EstimatedSalary'], axis = 1)\n",
    "df_x3[:2]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.428161\n",
      "         Iterations 6\n",
      "                           Logit Regression Results                           \n",
      "==============================================================================\n",
      "Dep. Variable:                 Exited   No. Observations:                10000\n",
      "Model:                          Logit   Df Residuals:                     9991\n",
      "Method:                           MLE   Df Model:                            8\n",
      "Date:                Wed, 28 Dec 2016   Pseudo R-squ.:                  0.1530\n",
      "Time:                        13:04:12   Log-Likelihood:                -4281.6\n",
      "converged:                       True   LL-Null:                       -5054.9\n",
      "                                        LLR p-value:                     0.000\n",
      "==================================================================================\n",
      "                     coef    std err          z      P>|z|      [95.0% Conf. Int.]\n",
      "----------------------------------------------------------------------------------\n",
      "const             -3.8959      0.236    -16.528      0.000        -4.358    -3.434\n",
      "Germany            0.7621      0.063     12.031      0.000         0.638     0.886\n",
      "Female             0.5290      0.054      9.710      0.000         0.422     0.636\n",
      "CreditScore       -0.0007      0.000     -2.378      0.017        -0.001    -0.000\n",
      "Age                0.0727      0.003     28.238      0.000         0.068     0.078\n",
      "Tenure            -0.0160      0.009     -1.710      0.087        -0.034     0.002\n",
      "Balance         2.653e-06   5.14e-07      5.162      0.000      1.65e-06  3.66e-06\n",
      "NumOfProducts     -0.1005      0.047     -2.132      0.033        -0.193    -0.008\n",
      "IsActiveMember    -1.0751      0.058    -18.644      0.000        -1.188    -0.962\n",
      "==================================================================================\n",
      "\n",
      "_____P-values____\n",
      "const              2.308408e-61\n",
      "Germany            2.434718e-33\n",
      "Female             2.744698e-22\n",
      "CreditScore        1.741371e-02\n",
      "Age               2.006986e-175\n",
      "Tenure             8.734004e-02\n",
      "Balance            2.440782e-07\n",
      "NumOfProducts      3.297139e-02\n",
      "IsActiveMember     1.407482e-77\n",
      "dtype: float64\n",
      "\n",
      "_____Highest p-value____\n",
      "\n",
      " Tenure    0.08734\n",
      "dtype: float64 \n",
      "\n",
      "\n",
      "____Confusion Matrix___\n",
      "[[ 7676.   287.]\n",
      " [ 1598.   439.]]\n",
      "\n",
      "Number of cases correctly predicted: 8115.0 (81.15 %)\n"
     ]
    }
   ],
   "source": [
    "model4 = smdis.Logit(df_y, df_x3).fit()\n",
    "print model4.summary()\n",
    "print \"\\n_____P-values____\"\n",
    "p = model4.pvalues\n",
    "print p\n",
    "print \"\\n_____Highest p-value____\"\n",
    "print \"\\n %s \\n\" % p[p ==max(p)]\n",
    "print \"\\n____Confusion Matrix___\"\n",
    "cm = model4.pred_table()\n",
    "print cm\n",
    "print \"\\nNumber of cases correctly predicted: %s (%s %%)\" % (cm[0][0] + cm[1][1], (cm[0][0] + cm[1][1])*100/np.sum(cm))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "New model without the variable with the highest p-value: \"Tenure\""
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>const</th>\n",
       "      <th>Germany</th>\n",
       "      <th>Female</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Age</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>IsActiveMember</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>619</td>\n",
       "      <td>42</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>608</td>\n",
       "      <td>41</td>\n",
       "      <td>83807.86</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   const  Germany  Female  CreditScore  Age   Balance  NumOfProducts  \\\n",
       "0      1        0       1          619   42      0.00              1   \n",
       "1      1        0       1          608   41  83807.86              1   \n",
       "\n",
       "   IsActiveMember  \n",
       "0               1  \n",
       "1               1  "
      ]
     },
     "execution_count": 15,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_x4 = df_x3.drop(['Tenure'], axis = 1)\n",
    "df_x4[:2]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.428307\n",
      "         Iterations 6\n",
      "                           Logit Regression Results                           \n",
      "==============================================================================\n",
      "Dep. Variable:                 Exited   No. Observations:                10000\n",
      "Model:                          Logit   Df Residuals:                     9992\n",
      "Method:                           MLE   Df Model:                            7\n",
      "Date:                Wed, 28 Dec 2016   Pseudo R-squ.:                  0.1527\n",
      "Time:                        13:04:13   Log-Likelihood:                -4283.1\n",
      "converged:                       True   LL-Null:                       -5054.9\n",
      "                                        LLR p-value:                     0.000\n",
      "==================================================================================\n",
      "                     coef    std err          z      P>|z|      [95.0% Conf. Int.]\n",
      "----------------------------------------------------------------------------------\n",
      "const             -3.9760      0.231    -17.200      0.000        -4.429    -3.523\n",
      "Germany            0.7608      0.063     12.014      0.000         0.637     0.885\n",
      "Female             0.5306      0.054      9.741      0.000         0.424     0.637\n",
      "CreditScore       -0.0007      0.000     -2.377      0.017        -0.001    -0.000\n",
      "Age                0.0727      0.003     28.237      0.000         0.068     0.078\n",
      "Balance         2.652e-06   5.14e-07      5.160      0.000      1.64e-06  3.66e-06\n",
      "NumOfProducts     -0.1010      0.047     -2.144      0.032        -0.193    -0.009\n",
      "IsActiveMember    -1.0718      0.058    -18.603      0.000        -1.185    -0.959\n",
      "==================================================================================\n",
      "\n",
      "_____P-values____\n",
      "const              2.658461e-66\n",
      "Germany            3.005769e-33\n",
      "Female             2.006779e-22\n",
      "CreditScore        1.745446e-02\n",
      "Age               2.037936e-175\n",
      "Balance            2.463940e-07\n",
      "NumOfProducts      3.199990e-02\n",
      "IsActiveMember     3.044455e-77\n",
      "dtype: float64\n",
      "\n",
      "_____Highest p-value____\n",
      "\n",
      " NumOfProducts    0.032\n",
      "dtype: float64 \n",
      "\n",
      "\n",
      "____Confusion Matrix___\n",
      "[[ 7676.   287.]\n",
      " [ 1601.   436.]]\n",
      "\n",
      "Number of cases correctly predicted: 8112.0 (81.12 %)\n"
     ]
    }
   ],
   "source": [
    "model5 = smdis.Logit(df_y, df_x4).fit()\n",
    "print model5.summary()\n",
    "print \"\\n_____P-values____\"\n",
    "p = model5.pvalues\n",
    "print p\n",
    "print \"\\n_____Highest p-value____\"\n",
    "print \"\\n %s \\n\" % p[p ==max(p)]\n",
    "print \"\\n____Confusion Matrix___\"\n",
    "cm = model5.pred_table()\n",
    "print cm\n",
    "print \"\\nNumber of cases correctly predicted: %s (%s %%)\" % (cm[0][0] + cm[1][1], (cm[0][0] + cm[1][1])*100/np.sum(cm))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Since the Accuracy decreases, the variable \"Tenure\" should be used in the model. \n",
    "Now, the variable with the highest p-value is \"NumOfProducts\" with 0.032, which is above of the defined threshold - 0.05.  "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 17,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>const</th>\n",
       "      <th>Germany</th>\n",
       "      <th>Female</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>IsActiveMember</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>619</td>\n",
       "      <td>42</td>\n",
       "      <td>2</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>608</td>\n",
       "      <td>41</td>\n",
       "      <td>1</td>\n",
       "      <td>83807.86</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>502</td>\n",
       "      <td>42</td>\n",
       "      <td>8</td>\n",
       "      <td>159660.80</td>\n",
       "      <td>3</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>699</td>\n",
       "      <td>39</td>\n",
       "      <td>1</td>\n",
       "      <td>0.00</td>\n",
       "      <td>2</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>850</td>\n",
       "      <td>43</td>\n",
       "      <td>2</td>\n",
       "      <td>125510.82</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   const  Germany  Female  CreditScore  Age  Tenure    Balance  NumOfProducts  \\\n",
       "0      1        0       1          619   42       2       0.00              1   \n",
       "1      1        0       1          608   41       1   83807.86              1   \n",
       "2      1        0       1          502   42       8  159660.80              3   \n",
       "3      1        0       1          699   39       1       0.00              2   \n",
       "4      1        0       1          850   43       2  125510.82              1   \n",
       "\n",
       "   IsActiveMember  \n",
       "0               1  \n",
       "1               1  \n",
       "2               0  \n",
       "3               0  \n",
       "4               1  "
      ]
     },
     "execution_count": 17,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_x3[:5]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#### Transforming Independent Variables"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Most common transformations:\n",
    "\n",
    "1. $$\\sqrt{x}$$\n",
    "2. $$x^2$$\n",
    "3. $$ln(x)$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Transform \"Balance\" variable with ln(x):\n",
    "\n",
    "Original value: Balance (in 1000$):\n",
    "\n",
    "        Bal2 = Bal1 + 1unit -->  Bal2 = Bal1 + 1000$\n",
    "        \n",
    "        Scenario1: Bal1 = 1000$\n",
    "            --> Bal2 = 1000$ + 1000$ = 2000$\n",
    "        Scenario2: Bal1 = 10000$\n",
    "            --> Bal2 = 10000$ + 1000$ = 11000$\n",
    "          \n",
    "Log10(Balance + 1):\n",
    "\n",
    "        log10(Bal2) = log10(Bal1) + 1unit --> Bal2 = Bal1*10\n",
    "        \n",
    "        Scenario1: Bal1 = 1000$\n",
    "            --> Bal2 = 1000*10 = 10000\n",
    "    Scenario2: Bal1 = 10000$\n",
    "            ---> Bal2 = 10000*10 = 100000\n",
    "            \n",
    "Using the original value, means that if someone that starts with a balance of 1000$ and has an increase of 1000$, and someone that starts with a balance of 10000$ and has the same amount of increasing, are two completly different things.\n",
    "\n",
    "Using the ln transformation, it has the same affect on any person regardless of their starting point. It always a 10 times increase so a unit increase has a consistent increase in the balance variable which is 10 times.\n",
    "\n",
    "So regardless of who we're segmenting we can say that the effect of a one unit increase in the new transformed variable is consistent throughout our population and that is much more powerful because that does not restrict the logistic regression.\n",
    "        "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 18,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>const</th>\n",
       "      <th>Germany</th>\n",
       "      <th>Female</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>IsActiveMember</th>\n",
       "      <th>Balance_log</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>619</td>\n",
       "      <td>42</td>\n",
       "      <td>2</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0.00000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>608</td>\n",
       "      <td>41</td>\n",
       "      <td>1</td>\n",
       "      <td>83807.86</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>4.92329</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   const  Germany  Female  CreditScore  Age  Tenure   Balance  NumOfProducts  \\\n",
       "0      1        0       1          619   42       2      0.00              1   \n",
       "1      1        0       1          608   41       1  83807.86              1   \n",
       "\n",
       "   IsActiveMember  Balance_log  \n",
       "0               1      0.00000  \n",
       "1               1      4.92329  "
      ]
     },
     "execution_count": 18,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_x4 = df_x3\n",
    "df_x4['Balance_log'] = df_x4['Balance'].map(lambda x: np.log10(x+1))\n",
    "df_x4[:2]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 19,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.428134\n",
      "         Iterations 6\n",
      "                           Logit Regression Results                           \n",
      "==============================================================================\n",
      "Dep. Variable:                 Exited   No. Observations:                10000\n",
      "Model:                          Logit   Df Residuals:                     9990\n",
      "Method:                           MLE   Df Model:                            9\n",
      "Date:                Wed, 28 Dec 2016   Pseudo R-squ.:                  0.1530\n",
      "Time:                        13:04:14   Log-Likelihood:                -4281.3\n",
      "converged:                       True   LL-Null:                       -5054.9\n",
      "                                        LLR p-value:                     0.000\n",
      "==================================================================================\n",
      "                     coef    std err          z      P>|z|      [95.0% Conf. Int.]\n",
      "----------------------------------------------------------------------------------\n",
      "const             -3.9147      0.237    -16.505      0.000        -4.380    -3.450\n",
      "Germany            0.7509      0.065     11.532      0.000         0.623     0.879\n",
      "Female             0.5286      0.054      9.701      0.000         0.422     0.635\n",
      "CreditScore       -0.0007      0.000     -2.387      0.017        -0.001    -0.000\n",
      "Age                0.0727      0.003     28.233      0.000         0.068     0.078\n",
      "Tenure            -0.0160      0.009     -1.706      0.088        -0.034     0.002\n",
      "Balance         1.871e-06   1.19e-06      1.568      0.117     -4.68e-07  4.21e-06\n",
      "NumOfProducts     -0.0957      0.048     -2.013      0.044        -0.189    -0.003\n",
      "IsActiveMember    -1.0754      0.058    -18.650      0.000        -1.188    -0.962\n",
      "Balance_log        0.0235      0.032      0.728      0.467        -0.040     0.087\n",
      "==================================================================================\n",
      "\n",
      "_____P-values____\n",
      "const              3.364882e-61\n",
      "Germany            9.111212e-31\n",
      "Female             2.971672e-22\n",
      "CreditScore        1.697954e-02\n",
      "Age               2.287651e-175\n",
      "Tenure             8.794792e-02\n",
      "Balance            1.169019e-01\n",
      "NumOfProducts      4.409600e-02\n",
      "IsActiveMember     1.262368e-77\n",
      "Balance_log        4.668374e-01\n",
      "dtype: float64\n",
      "\n",
      "_____Highest p-value____\n",
      "\n",
      " Balance_log    0.466837\n",
      "dtype: float64 \n",
      "\n",
      "\n",
      "____Confusion Matrix___\n",
      "[[ 7681.   282.]\n",
      " [ 1597.   440.]]\n",
      "\n",
      "Number of cases correctly predicted: 8121.0 (81.21 %)\n"
     ]
    }
   ],
   "source": [
    "model6 = smdis.Logit(df_y, df_x4).fit()\n",
    "print model6.summary()\n",
    "print \"\\n_____P-values____\"\n",
    "p = model6.pvalues\n",
    "print p\n",
    "print \"\\n_____Highest p-value____\"\n",
    "print \"\\n %s \\n\" % p[p ==max(p)]\n",
    "print \"\\n____Confusion Matrix___\"\n",
    "cm = model6.pred_table()\n",
    "print cm\n",
    "print \"\\nNumber of cases correctly predicted: %s (%s %%)\" % (cm[0][0] + cm[1][1], (cm[0][0] + cm[1][1])*100/np.sum(cm))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The accuracy is improved!"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#### Derived Variables\n",
    "\n",
    "Sometimes it is useful too account for some effects, for example in our case, the balance and age might be correlated, because the older a person is the more whealth he/she can accumulate. \n",
    "\n",
    "So a new variable can be created:\n",
    "\n",
    "$$WealthAccumlation = \\frac{Balance}{Age}$$"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 20,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>const</th>\n",
       "      <th>Germany</th>\n",
       "      <th>Female</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>IsActiveMember</th>\n",
       "      <th>Balance_log</th>\n",
       "      <th>WealthAccumulation</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>619</td>\n",
       "      <td>42</td>\n",
       "      <td>2</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0.00000</td>\n",
       "      <td>0.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>608</td>\n",
       "      <td>41</td>\n",
       "      <td>1</td>\n",
       "      <td>83807.86</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>4.92329</td>\n",
       "      <td>2044.094146</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   const  Germany  Female  CreditScore  Age  Tenure   Balance  NumOfProducts  \\\n",
       "0      1        0       1          619   42       2      0.00              1   \n",
       "1      1        0       1          608   41       1  83807.86              1   \n",
       "\n",
       "   IsActiveMember  Balance_log  WealthAccumulation  \n",
       "0               1      0.00000            0.000000  \n",
       "1               1      4.92329         2044.094146  "
      ]
     },
     "execution_count": 20,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_x5 = df_x4\n",
    "df_x5['WealthAccumulation'] = df_x5['Balance']/ df_x5['Age']\n",
    "df_x5[:2]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 21,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.427323\n",
      "         Iterations 6\n",
      "                           Logit Regression Results                           \n",
      "==============================================================================\n",
      "Dep. Variable:                 Exited   No. Observations:                10000\n",
      "Model:                          Logit   Df Residuals:                     9989\n",
      "Method:                           MLE   Df Model:                           10\n",
      "Date:                Wed, 28 Dec 2016   Pseudo R-squ.:                  0.1546\n",
      "Time:                        13:04:15   Log-Likelihood:                -4273.2\n",
      "converged:                       True   LL-Null:                       -5054.9\n",
      "                                        LLR p-value:                     0.000\n",
      "======================================================================================\n",
      "                         coef    std err          z      P>|z|      [95.0% Conf. Int.]\n",
      "--------------------------------------------------------------------------------------\n",
      "const                 -3.4253      0.266    -12.896      0.000        -3.946    -2.905\n",
      "Germany                0.7534      0.066     11.483      0.000         0.625     0.882\n",
      "Female                 0.5289      0.055      9.693      0.000         0.422     0.636\n",
      "CreditScore           -0.0007      0.000     -2.326      0.020        -0.001    -0.000\n",
      "Age                    0.0611      0.004     15.903      0.000         0.054     0.069\n",
      "Tenure                -0.0165      0.009     -1.762      0.078        -0.035     0.002\n",
      "Balance             8.362e-06   2.02e-06      4.143      0.000      4.41e-06  1.23e-05\n",
      "NumOfProducts         -0.1011      0.048     -2.119      0.034        -0.195    -0.008\n",
      "IsActiveMember        -1.0601      0.058    -18.349      0.000        -1.173    -0.947\n",
      "Balance_log            0.0213      0.032      0.657      0.511        -0.042     0.085\n",
      "WealthAccumulation    -0.0003   6.51e-05     -3.954      0.000        -0.000    -0.000\n",
      "======================================================================================\n",
      "\n",
      "_____P-values____\n",
      "const                 4.756747e-38\n",
      "Germany               1.599880e-30\n",
      "Female                3.244594e-22\n",
      "CreditScore           2.000443e-02\n",
      "Age                   6.030631e-57\n",
      "Tenure                7.801725e-02\n",
      "Balance               3.428919e-05\n",
      "NumOfProducts         3.411316e-02\n",
      "IsActiveMember        3.376037e-75\n",
      "Balance_log           5.111832e-01\n",
      "WealthAccumulation    7.673375e-05\n",
      "dtype: float64\n",
      "\n",
      "_____Highest p-value____\n",
      "\n",
      " Balance_log    0.511183\n",
      "dtype: float64 \n",
      "\n",
      "\n",
      "____Confusion Matrix___\n",
      "[[ 7662.   301.]\n",
      " [ 1586.   451.]]\n",
      "\n",
      "Number of cases correctly predicted: 8113.0 (81.13 %)\n"
     ]
    }
   ],
   "source": [
    "model7 = smdis.Logit(df_y, df_x5).fit()\n",
    "print model7.summary()\n",
    "print \"\\n_____P-values____\"\n",
    "p = model7.pvalues\n",
    "print p\n",
    "print \"\\n_____Highest p-value____\"\n",
    "print \"\\n %s \\n\" % p[p ==max(p)]\n",
    "print \"\\n____Confusion Matrix___\"\n",
    "cm = model7.pred_table()\n",
    "print cm\n",
    "print \"\\nNumber of cases correctly predicted: %s (%s %%)\" % (cm[0][0] + cm[1][1], (cm[0][0] + cm[1][1])*100/np.sum(cm))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "This new variable had a negative effect in the model accuracy.\n",
    "\n",
    "However, one should have in consideration that might be some collinearity effects because wealth accumulation includes the variables balance and age that are already in the model. So basically this means that this new variable might be somehow correlated with the other two and including all of them in the model might cause some damage, so on of them should be excluded.\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#### Multicollinearity\n",
    "\n",
    "Multicollinearity is a phenomenon in which two or more predictor variables in a multiple regression model are highly correlated, meaning that one can be linearly predicted from the others with a substantial degree of accuracy.\n",
    "\n",
    "The \"Variance Inflation Factors - VIF\" can be used to measure the degree of multicollinearity.\n",
    "\n",
    "Minimum possible value = 1.0\n",
    "Values > 5 may indicate a collinearity problem.\n",
    "\n",
    "$$VIF(j) = \\frac{1}{( 1 - R(j)^2)}$$\n",
    "\n",
    "where R(j) is the multiple correlarion coefficient between variable j and the other independent variables\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 22,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "const 91.4773722803\n",
      "Germany 1.27101224421\n",
      "Female 1.00321582613\n",
      "CreditScore 1.00104450767\n",
      "Age 2.10487620512\n",
      "Tenure 1.00161073156\n",
      "Balance 20.8895673667\n",
      "NumOfProducts 1.15312466504\n",
      "IsActiveMember 1.01384972933\n",
      "Balance_log 8.72881202924\n",
      "WealthAccumulation 14.5598595824\n"
     ]
    }
   ],
   "source": [
    "i = 0\n",
    "for column in df_x5.columns:\n",
    "    \n",
    "    print column + \" %s\" % outliers.variance_inflation_factor(df_x5.values, i)\n",
    "    i += 1"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Rerun without the \"Balance_log\" variable:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 23,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "df_x6 = df_x5.drop('Balance_log', axis = 1)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 24,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.427344\n",
      "         Iterations 6\n",
      "                           Logit Regression Results                           \n",
      "==============================================================================\n",
      "Dep. Variable:                 Exited   No. Observations:                10000\n",
      "Model:                          Logit   Df Residuals:                     9990\n",
      "Method:                           MLE   Df Model:                            9\n",
      "Date:                Wed, 28 Dec 2016   Pseudo R-squ.:                  0.1546\n",
      "Time:                        13:04:15   Log-Likelihood:                -4273.4\n",
      "converged:                       True   LL-Null:                       -5054.9\n",
      "                                        LLR p-value:                     0.000\n",
      "======================================================================================\n",
      "                         coef    std err          z      P>|z|      [95.0% Conf. Int.]\n",
      "--------------------------------------------------------------------------------------\n",
      "const                 -3.4089      0.264    -12.904      0.000        -3.927    -2.891\n",
      "Germany                0.7636      0.064     11.967      0.000         0.639     0.889\n",
      "Female                 0.5292      0.055      9.699      0.000         0.422     0.636\n",
      "CreditScore           -0.0007      0.000     -2.318      0.020        -0.001    -0.000\n",
      "Age                    0.0612      0.004     15.938      0.000         0.054     0.069\n",
      "Tenure                -0.0165      0.009     -1.766      0.077        -0.035     0.002\n",
      "Balance             9.078e-06    1.7e-06      5.351      0.000      5.75e-06  1.24e-05\n",
      "NumOfProducts         -0.1054      0.047     -2.229      0.026        -0.198    -0.013\n",
      "IsActiveMember        -1.0598      0.058    -18.343      0.000        -1.173    -0.947\n",
      "WealthAccumulation    -0.0003   6.49e-05     -3.965      0.000        -0.000    -0.000\n",
      "======================================================================================\n",
      "\n",
      "_____P-values____\n",
      "const                 4.282278e-38\n",
      "Germany               5.318191e-33\n",
      "Female                3.042840e-22\n",
      "CreditScore           2.047417e-02\n",
      "Age                   3.455661e-57\n",
      "Tenure                7.744433e-02\n",
      "Balance               8.770661e-08\n",
      "NumOfProducts         2.578202e-02\n",
      "IsActiveMember        3.744959e-75\n",
      "WealthAccumulation    7.325518e-05\n",
      "dtype: float64\n",
      "\n",
      "_____Highest p-value____\n",
      "\n",
      " Tenure    0.077444\n",
      "dtype: float64 \n",
      "\n",
      "\n",
      "____Confusion Matrix___\n",
      "[[ 7672.   291.]\n",
      " [ 1586.   451.]]\n",
      "\n",
      "Number of cases correctly predicted: 8123.0 (81.23 %)\n"
     ]
    }
   ],
   "source": [
    "model8 = smdis.Logit(df_y, df_x6).fit()\n",
    "print model8.summary()\n",
    "print \"\\n_____P-values____\"\n",
    "p = model8.pvalues\n",
    "print p\n",
    "print \"\\n_____Highest p-value____\"\n",
    "print \"\\n %s \\n\" % p[p ==max(p)]\n",
    "print \"\\n____Confusion Matrix___\"\n",
    "cm = model8.pred_table()\n",
    "print cm\n",
    "print \"\\nNumber of cases correctly predicted: %s (%s %%)\" % (cm[0][0] + cm[1][1], (cm[0][0] + cm[1][1])*100/np.sum(cm))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 25,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "const 90.6320775047\n",
      "Germany 1.2161676569\n",
      "Female 1.00302785174\n",
      "CreditScore 1.00097995156\n",
      "Age 2.10477505278\n",
      "Tenure 1.00153406128\n",
      "Balance 14.0245653407\n",
      "NumOfProducts 1.12330226029\n",
      "IsActiveMember 1.01353234553\n",
      "WealthAccumulation 14.5598562679\n"
     ]
    }
   ],
   "source": [
    "i = 0\n",
    "\n",
    "for column in df_x6.columns:\n",
    "    \n",
    "    print column + \" %s\" % outliers.variance_inflation_factor(df_x6.values, i)\n",
    "    i += 1"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Create new variable log for wealth accumulation:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 26,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>const</th>\n",
       "      <th>Germany</th>\n",
       "      <th>Female</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>IsActiveMember</th>\n",
       "      <th>Balance_log</th>\n",
       "      <th>WealthAccumulation</th>\n",
       "      <th>WealthAccumulation_log</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>619</td>\n",
       "      <td>42</td>\n",
       "      <td>2</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0.00000</td>\n",
       "      <td>0.000000</td>\n",
       "      <td>0.000000</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>608</td>\n",
       "      <td>41</td>\n",
       "      <td>1</td>\n",
       "      <td>83807.86</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>4.92329</td>\n",
       "      <td>2044.094146</td>\n",
       "      <td>3.310713</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   const  Germany  Female  CreditScore  Age  Tenure   Balance  NumOfProducts  \\\n",
       "0      1        0       1          619   42       2      0.00              1   \n",
       "1      1        0       1          608   41       1  83807.86              1   \n",
       "\n",
       "   IsActiveMember  Balance_log  WealthAccumulation  WealthAccumulation_log  \n",
       "0               1      0.00000            0.000000                0.000000  \n",
       "1               1      4.92329         2044.094146                3.310713  "
      ]
     },
     "execution_count": 26,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_x7 = df_x5\n",
    "df_x7['WealthAccumulation_log'] = df_x7['WealthAccumulation'].map(lambda x: np.log10(x + 1))\n",
    "df_x7[:2]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 27,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df_x7 = df_x7.drop(['Balance', 'WealthAccumulation'], axis = 1)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 28,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.427891\n",
      "         Iterations 6\n",
      "                           Logit Regression Results                           \n",
      "==============================================================================\n",
      "Dep. Variable:                 Exited   No. Observations:                10000\n",
      "Model:                          Logit   Df Residuals:                     9990\n",
      "Method:                           MLE   Df Model:                            9\n",
      "Date:                Wed, 28 Dec 2016   Pseudo R-squ.:                  0.1535\n",
      "Time:                        13:04:16   Log-Likelihood:                -4278.9\n",
      "converged:                       True   LL-Null:                       -5054.9\n",
      "                                        LLR p-value:                     0.000\n",
      "==========================================================================================\n",
      "                             coef    std err          z      P>|z|      [95.0% Conf. Int.]\n",
      "------------------------------------------------------------------------------------------\n",
      "const                     -3.5735      0.267    -13.362      0.000        -4.098    -3.049\n",
      "Germany                    0.7516      0.065     11.492      0.000         0.623     0.880\n",
      "Female                     0.5263      0.055      9.655      0.000         0.420     0.633\n",
      "CreditScore               -0.0007      0.000     -2.386      0.017        -0.001    -0.000\n",
      "Age                        0.0647      0.004     16.631      0.000         0.057     0.072\n",
      "Tenure                    -0.0162      0.009     -1.729      0.084        -0.035     0.002\n",
      "NumOfProducts             -0.0983      0.048     -2.063      0.039        -0.192    -0.005\n",
      "IsActiveMember            -1.0687      0.058    -18.516      0.000        -1.182    -0.956\n",
      "Balance_log                0.8699      0.297      2.932      0.003         0.288     1.451\n",
      "WealthAccumulation_log    -1.1762      0.435     -2.702      0.007        -2.029    -0.323\n",
      "==========================================================================================\n",
      "\n",
      "_____P-values____\n",
      "const                     1.007546e-40\n",
      "Germany                   1.441579e-30\n",
      "Female                    4.661027e-22\n",
      "CreditScore               1.704065e-02\n",
      "Age                       4.171785e-62\n",
      "Tenure                    8.382654e-02\n",
      "NumOfProducts             3.907377e-02\n",
      "IsActiveMember            1.520894e-76\n",
      "Balance_log               3.373142e-03\n",
      "WealthAccumulation_log    6.887931e-03\n",
      "dtype: float64\n",
      "\n",
      "_____Highest p-value____\n",
      "\n",
      " Tenure    0.083827\n",
      "dtype: float64 \n",
      "\n",
      "\n",
      "____Confusion Matrix___\n",
      "[[ 7685.   278.]\n",
      " [ 1597.   440.]]\n",
      "\n",
      "Number of cases correctly predicted: 8125.0 (81.25 %)\n"
     ]
    }
   ],
   "source": [
    "model9 = smdis.Logit(df_y, df_x7).fit()\n",
    "print model9.summary()\n",
    "print \"\\n_____P-values____\"\n",
    "p = model9.pvalues\n",
    "print p\n",
    "print \"\\n_____Highest p-value____\"\n",
    "print \"\\n %s \\n\" % p[p ==max(p)]\n",
    "print \"\\n____Confusion Matrix___\"\n",
    "cm = model9.pred_table()\n",
    "print cm\n",
    "print \"\\nNumber of cases correctly predicted: %s (%s %%)\" % (cm[0][0] + cm[1][1], (cm[0][0] + cm[1][1])*100/np.sum(cm))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 29,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "const 93.5608074531\n",
      "Germany 1.26986359735\n",
      "Female 1.00282038022\n",
      "CreditScore 1.00102945704\n",
      "Age 2.26479838945\n",
      "Tenure 1.00165459162\n",
      "NumOfProducts 1.15243000098\n",
      "IsActiveMember 1.01168357579\n",
      "Balance_log 705.940971113\n",
      "WealthAccumulation_log 704.739971273\n"
     ]
    }
   ],
   "source": [
    "i = 0\n",
    "\n",
    "for column in df_x7.columns:\n",
    "    \n",
    "    print column + \" %s\" % outliers.variance_inflation_factor(df_x7.values, i)\n",
    "    i += 1"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "\"Balance_log\" and \"WealthAccumulation_log\" have too large values which means that these two variables are basically the same thing. So one of these variables must be excluded!"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 30,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df_x8 = df_x7.drop(['Balance_log'], axis = 1)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 31,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.428322\n",
      "         Iterations 6\n",
      "                           Logit Regression Results                           \n",
      "==============================================================================\n",
      "Dep. Variable:                 Exited   No. Observations:                10000\n",
      "Model:                          Logit   Df Residuals:                     9991\n",
      "Method:                           MLE   Df Model:                            8\n",
      "Date:                Wed, 28 Dec 2016   Pseudo R-squ.:                  0.1527\n",
      "Time:                        13:04:18   Log-Likelihood:                -4283.2\n",
      "converged:                       True   LL-Null:                       -5054.9\n",
      "                                        LLR p-value:                     0.000\n",
      "==========================================================================================\n",
      "                             coef    std err          z      P>|z|      [95.0% Conf. Int.]\n",
      "------------------------------------------------------------------------------------------\n",
      "const                     -3.9325      0.239    -16.467      0.000        -4.401    -3.464\n",
      "Germany                    0.7515      0.065     11.550      0.000         0.624     0.879\n",
      "Female                     0.5266      0.054      9.671      0.000         0.420     0.633\n",
      "CreditScore               -0.0007      0.000     -2.409      0.016        -0.001    -0.000\n",
      "Age                        0.0733      0.003     28.398      0.000         0.068     0.078\n",
      "Tenure                    -0.0159      0.009     -1.697      0.090        -0.034     0.002\n",
      "NumOfProducts             -0.0967      0.048     -2.034      0.042        -0.190    -0.004\n",
      "IsActiveMember            -1.0763      0.058    -18.672      0.000        -1.189    -0.963\n",
      "WealthAccumulation_log     0.0985      0.020      4.812      0.000         0.058     0.139\n",
      "==========================================================================================\n",
      "\n",
      "_____P-values____\n",
      "const                      6.349678e-61\n",
      "Germany                    7.379902e-31\n",
      "Female                     4.015783e-22\n",
      "CreditScore                1.598070e-02\n",
      "Age                       2.132441e-177\n",
      "Tenure                     8.978088e-02\n",
      "NumOfProducts              4.195258e-02\n",
      "IsActiveMember             8.443410e-78\n",
      "WealthAccumulation_log     1.490621e-06\n",
      "dtype: float64\n",
      "\n",
      "_____Highest p-value____\n",
      "\n",
      " Tenure    0.089781\n",
      "dtype: float64 \n",
      "\n",
      "\n",
      "____Confusion Matrix___\n",
      "[[ 7688.   275.]\n",
      " [ 1597.   440.]]\n",
      "\n",
      "Number of cases correctly predicted: 8128.0 (81.28 %)\n"
     ]
    }
   ],
   "source": [
    "model10 = smdis.Logit(df_y, df_x8).fit()\n",
    "print model10.summary()\n",
    "print \"\\n_____P-values____\"\n",
    "p = model10.pvalues\n",
    "print p\n",
    "print \"\\n_____Highest p-value____\"\n",
    "print \"\\n %s \\n\" % p[p ==max(p)]\n",
    "print \"\\n____Confusion Matrix___\"\n",
    "cm = model10.pred_table()\n",
    "print cm\n",
    "print \"\\nNumber of cases correctly predicted: %s (%s %%)\" % (cm[0][0] + cm[1][1], (cm[0][0] + cm[1][1])*100/np.sum(cm))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Correlation between variables:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 32,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Age</th>\n",
       "      <th>Balance_log</th>\n",
       "      <th>WealthAccumulation_log</th>\n",
       "      <th>WealthAccumulation</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>Age</th>\n",
       "      <td>1.000000</td>\n",
       "      <td>0.034530</td>\n",
       "      <td>-0.007524</td>\n",
       "      <td>-0.246293</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Balance_log</th>\n",
       "      <td>0.034530</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>0.998404</td>\n",
       "      <td>0.865141</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>WealthAccumulation_log</th>\n",
       "      <td>-0.007524</td>\n",
       "      <td>0.998404</td>\n",
       "      <td>1.000000</td>\n",
       "      <td>0.888872</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>WealthAccumulation</th>\n",
       "      <td>-0.246293</td>\n",
       "      <td>0.865141</td>\n",
       "      <td>0.888872</td>\n",
       "      <td>1.000000</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "                             Age  Balance_log  WealthAccumulation_log  \\\n",
       "Age                     1.000000     0.034530               -0.007524   \n",
       "Balance_log             0.034530     1.000000                0.998404   \n",
       "WealthAccumulation_log -0.007524     0.998404                1.000000   \n",
       "WealthAccumulation     -0.246293     0.865141                0.888872   \n",
       "\n",
       "                        WealthAccumulation  \n",
       "Age                              -0.246293  \n",
       "Balance_log                       0.865141  \n",
       "WealthAccumulation_log            0.888872  \n",
       "WealthAccumulation                1.000000  "
      ]
     },
     "execution_count": 32,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_x5[['Age', 'Balance_log', 'WealthAccumulation_log', 'WealthAccumulation']].corr()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "As one can observe, the \"WealthAccumulation_log\" and \"Balance_log\" variables are highly correlated - they are basically the same which is very bad for the model.\n",
    " \n",
    "Thumb rule: anything 0.9 is very high correlation. Correlations above 0.5 should be addressed."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 33,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "df_final = df_x5.drop(['Balance', 'WealthAccumulation', 'WealthAccumulation_log'], axis = 1)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 34,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.428257\n",
      "         Iterations 6\n",
      "                           Logit Regression Results                           \n",
      "==============================================================================\n",
      "Dep. Variable:                 Exited   No. Observations:                10000\n",
      "Model:                          Logit   Df Residuals:                     9991\n",
      "Method:                           MLE   Df Model:                            8\n",
      "Date:                Wed, 28 Dec 2016   Pseudo R-squ.:                  0.1528\n",
      "Time:                        13:04:19   Log-Likelihood:                -4282.6\n",
      "converged:                       True   LL-Null:                       -5054.9\n",
      "                                        LLR p-value:                     0.000\n",
      "==================================================================================\n",
      "                     coef    std err          z      P>|z|      [95.0% Conf. Int.]\n",
      "----------------------------------------------------------------------------------\n",
      "const             -3.9126      0.237    -16.497      0.000        -4.377    -3.448\n",
      "Germany            0.7476      0.065     11.492      0.000         0.620     0.875\n",
      "Female             0.5267      0.054      9.672      0.000         0.420     0.633\n",
      "CreditScore       -0.0007      0.000     -2.408      0.016        -0.001    -0.000\n",
      "Age                0.0727      0.003     28.221      0.000         0.068     0.078\n",
      "Tenure            -0.0159      0.009     -1.699      0.089        -0.034     0.002\n",
      "NumOfProducts     -0.0950      0.048     -1.999      0.046        -0.188    -0.002\n",
      "IsActiveMember    -1.0758      0.058    -18.662      0.000        -1.189    -0.963\n",
      "Balance_log        0.0690      0.014      4.945      0.000         0.042     0.096\n",
      "==================================================================================\n",
      "\n",
      "_____P-values____\n",
      "const              3.835044e-61\n",
      "Germany            1.441009e-30\n",
      "Female             3.970390e-22\n",
      "CreditScore        1.604464e-02\n",
      "Age               3.239286e-175\n",
      "Tenure             8.932416e-02\n",
      "NumOfProducts      4.562540e-02\n",
      "IsActiveMember     1.011855e-77\n",
      "Balance_log        7.619575e-07\n",
      "dtype: float64\n",
      "\n",
      "_____Highest p-value____\n",
      "\n",
      " Tenure    0.089324\n",
      "dtype: float64 \n",
      "\n",
      "\n",
      "____Confusion Matrix___\n",
      "[[ 7687.   276.]\n",
      " [ 1597.   440.]]\n",
      "\n",
      "Number of cases correctly predicted: 8127.0 (81.27 %)\n"
     ]
    }
   ],
   "source": [
    "model11 = smdis.Logit(df_y, df_final).fit()\n",
    "print model11.summary()\n",
    "print \"\\n_____P-values____\"\n",
    "p = model11.pvalues\n",
    "print p\n",
    "print \"\\n_____Highest p-value____\"\n",
    "print \"\\n %s \\n\" % p[p ==max(p)]\n",
    "print \"\\n____Confusion Matrix___\"\n",
    "cm = model11.pred_table()\n",
    "print cm\n",
    "print \"\\nNumber of cases correctly predicted: %s (%s %%)\" % (cm[0][0] + cm[1][1], (cm[0][0] + cm[1][1])*100/np.sum(cm))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 35,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "const 76.3936634172\n",
      "Germany 1.26934389528\n",
      "Female 1.00281465894\n",
      "CreditScore 1.00102204693\n",
      "Age 1.01171547993\n",
      "Tenure 1.0014367308\n",
      "NumOfProducts 1.15145597018\n",
      "IsActiveMember 1.01028026511\n",
      "Balance_log 1.42007014313\n"
     ]
    }
   ],
   "source": [
    "i = 0\n",
    "\n",
    "for column in df_final.columns:\n",
    "    \n",
    "    print column + \" %s\" % outliers.variance_inflation_factor(df_final.values, i)\n",
    "    i += 1"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Now, none of the variables presents multicollinearity."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#### CAP - Cumulative Accuracy Profile Curve"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 36,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "df_cap = df[['RowNumber', 'Exited']]\n",
    "df_cap['phat'] = model11.predict()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 37,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>RowNumber</th>\n",
       "      <th>Exited</th>\n",
       "      <th>phat</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>4815</th>\n",
       "      <td>4816</td>\n",
       "      <td>0</td>\n",
       "      <td>0.930669</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3531</th>\n",
       "      <td>3532</td>\n",
       "      <td>1</td>\n",
       "      <td>0.928311</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9587</th>\n",
       "      <td>9588</td>\n",
       "      <td>0</td>\n",
       "      <td>0.923303</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7499</th>\n",
       "      <td>7500</td>\n",
       "      <td>1</td>\n",
       "      <td>0.906178</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9555</th>\n",
       "      <td>9556</td>\n",
       "      <td>1</td>\n",
       "      <td>0.904457</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "      RowNumber  Exited      phat\n",
       "4815       4816       0  0.930669\n",
       "3531       3532       1  0.928311\n",
       "9587       9588       0  0.923303\n",
       "7499       7500       1  0.906178\n",
       "9555       9556       1  0.904457"
      ]
     },
     "execution_count": 37,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_cap.sort('phat', ascending=False)[:5]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 153,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Total Exited: 2037\n",
      "Total number of clients: 10000\n",
      "Ratio of exits: 0.2037 \n"
     ]
    }
   ],
   "source": [
    "Total_Exited = sum(df_cap['Exited'])\n",
    "print \"Total Exited: %s\" % Total_Exited\n",
    "Total_Records = len(df)\n",
    "print \"Total number of clients: %s\" % Total_Records\n",
    "Exit_Ratio = Total_Exited/Total_Records\n",
    "print \"Ratio of exits: %s \" % Exit_Ratio"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "First, I select the clients that have a higher probability."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 111,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "df_cap['total_selected'] = range(1,len(df_cap)+1)\n",
    "df_cap['total_selected_per'] = df_cap['total_selected'].map(lambda x: round(x*100/Total_Records, 1))\n",
    "df_cap['random_selection'] = df_cap['total_selected'].map(lambda x: x*Exit_Ratio)\n",
    "df_cap['random_selection_per'] = df_cap['total_selected'].map(lambda x: round(x*100/Total_Records, 1))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 112,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df_cap = df_cap.sort('phat', ascending=False)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 113,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "df_cap['model_select'] = df_cap.Exited.cumsum()\n",
    "df_cap['model_select_per'] = df_cap['model_select'].map(lambda x: round(x*100/Total_Exited, 1))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 114,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>RowNumber</th>\n",
       "      <th>Exited</th>\n",
       "      <th>phat</th>\n",
       "      <th>total_selected</th>\n",
       "      <th>total_selected_per</th>\n",
       "      <th>random_selection</th>\n",
       "      <th>random_selection_per</th>\n",
       "      <th>model_select</th>\n",
       "      <th>model_select_per</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>4815</th>\n",
       "      <td>4816</td>\n",
       "      <td>0</td>\n",
       "      <td>0.930669</td>\n",
       "      <td>1</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.2037</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3531</th>\n",
       "      <td>3532</td>\n",
       "      <td>1</td>\n",
       "      <td>0.928311</td>\n",
       "      <td>2</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.4074</td>\n",
       "      <td>0.0</td>\n",
       "      <td>1</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9587</th>\n",
       "      <td>9588</td>\n",
       "      <td>0</td>\n",
       "      <td>0.923303</td>\n",
       "      <td>3</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.6111</td>\n",
       "      <td>0.0</td>\n",
       "      <td>1</td>\n",
       "      <td>0.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7499</th>\n",
       "      <td>7500</td>\n",
       "      <td>1</td>\n",
       "      <td>0.906178</td>\n",
       "      <td>4</td>\n",
       "      <td>0.0</td>\n",
       "      <td>0.8148</td>\n",
       "      <td>0.0</td>\n",
       "      <td>2</td>\n",
       "      <td>0.1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9555</th>\n",
       "      <td>9556</td>\n",
       "      <td>1</td>\n",
       "      <td>0.904457</td>\n",
       "      <td>5</td>\n",
       "      <td>0.1</td>\n",
       "      <td>1.0185</td>\n",
       "      <td>0.1</td>\n",
       "      <td>3</td>\n",
       "      <td>0.1</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "      RowNumber  Exited      phat  total_selected  total_selected_per  \\\n",
       "4815       4816       0  0.930669               1                 0.0   \n",
       "3531       3532       1  0.928311               2                 0.0   \n",
       "9587       9588       0  0.923303               3                 0.0   \n",
       "7499       7500       1  0.906178               4                 0.0   \n",
       "9555       9556       1  0.904457               5                 0.1   \n",
       "\n",
       "      random_selection  random_selection_per  model_select  model_select_per  \n",
       "4815            0.2037                   0.0             0               0.0  \n",
       "3531            0.4074                   0.0             1               0.0  \n",
       "9587            0.6111                   0.0             1               0.0  \n",
       "7499            0.8148                   0.0             2               0.1  \n",
       "9555            1.0185                   0.1             3               0.1  "
      ]
     },
     "execution_count": 114,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_cap[:5]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 115,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "image/png": "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\nD2jfvn2RfVOmTKF79+6Vf1El6NatG7fddhuDBg1izZo1fPDBB7z++usVPo8GiBJkZen4h1Ago4SP\nt9t2p7CqiFe3wpqR4IjQNSxCTMGqm7169SIrK4uHHnqIhx56iOuuu67E42+//Xbq1KlD3bp1z/tv\nwWqWpXnggQdo2LAhN998M5s3b3bt37JlC23bti1SU2RkJDt27KBFixb89NNPnDhxgs8//5w2bdqw\nb98+Fi5cyFNPPVWh13rHHXewZ88etm8/9xk0b948+vTpU+Lxjz/++HmvseD7K664osxrTZkyheTk\nZO6++25eeukl6tevX6FaQQNEiXQAZXDr/n53vcMiUHw2GUblwWFrPjkNDjaq6gyuVZgJs1+/frRo\n0YKrrrqK9PR0xo4dW+qxS5cu5dixYxw9evS8/y5ZsqTU582fP589e/aQkpJCly5duOmmmzh58iRg\njRGIjY0tcnzt2rU5deoUdevWZcSIEXTt2pXly5czefJkBg8ezIQJE1i8eDFdunShe/fupKWllfs6\nq1evTo8ePZg3bx5gBZeUlBRuvfXWEo9/7bXXznuNBd8XtNiU5oILLqBNmzZkZWVVuoVDA0QJdBXO\n4FQwSPKjXz6yuxRVnrM14OVd8M0/wIRrq4M/SErC9T+i8FdZAcKd49z0t7/9jS1btjBo0CAiIiKq\ndK6SdOrUicjISKKiohg+fDgXXHABa9euBaBmzZquMFHgxIkT1KpVC4B7772X//73vyxbtozNmzcT\nFRXFFVdcwVNPPcWyZcu4++67+cc//uFWHQ8++CDz588HrNaHHj16eOX1zps3j5SUFG688Ub++c9/\nVuocGiCKcThyyc7e5drWWSiDg4wSYsfHln+gB+haGFX0yRvwwik4nghocFDWnQNDhgyhX79+JCUl\nFbkDori//OUv1KpVy3U3ReGv0v6SL4lz4CAAbdq0YdOmTa7HfvvtN3Jzc2nZsujnQ3Z2NiNGjOCl\nl15i586dNGvWjBo1atChQwd++uknt67bsWNHqlevztq1a5k/fz69e/cu9djHHnusxNdaq1YtLrvs\nslKfd+jQIZ588kneeustpk+fzqJFi1i3bp1b9RWmd2EUk5m5g/XrrW6LyMgmdOqU6pXrKN/Izc+l\n+tjqPr2mroVRSacbwps/wql4QIODNwTqXRj9+vUjKyuL+fPn8+ijj3L8+HHef/99j50/NTWV1NRU\n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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x10c69d890>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "df_cap = df_cap.sort('RowNumber')\n",
    "plt.plot(df_cap['total_selected_per'], df_cap['random_selection_per'], label = 'random model' )\n",
    "plt.plot(df_cap['total_selected_per'], df_cap['model_select_per'], label = 'Good Model' )\n",
    "plt.plot([50, 50], [80, 0], 'r--', lw=1, label = 'x = 50%, y = X')\n",
    "plt.plot([0, 50], [80, 80], 'r--', lw=1)\n",
    "plt.plot([10, 100], [99, 99], 'y-', lw=3, label = 'perfect model')\n",
    "plt.plot([0, 10], [0, 99], 'y-', lw=3)\n",
    "plt.legend(bbox_to_anchor=(1, 1), loc='upper left', ncol=1)\n",
    "plt.xlabel('Total Contacted')\n",
    "plt.ylabel('Total Purchased')\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The closer the green line is to the yellow line, the better the model. The closer to the blue line, the worse. \n",
    "\n",
    "__Accuracy Ratio__:\n",
    "    - Take the area under the perfect model (yellow line) to the random model (blue line) - \n",
    "$$a_p$$\n",
    "    - Take the area under the good model (green line) to the random model (blue line) - \n",
    "    \n",
    "$$a_r$$\n",
    "    - So the accuracy ratio:\n",
    "    \n",
    "$$AR = \\frac{a_r}{a_p}$$\n",
    "\n",
    "that is between 0 and 1, and the closer the ratio is to 1 the better.\n",
    "\n",
    "__Rule of thumb:__\n",
    "\n",
    "Another way of evaluating the model is to look to the red line, x = 50%, and y = X:\n",
    "\n",
    "If:\n",
    "\n",
    "    90% < X < 100% -> Too Good (overfitting?)\n",
    "    80% < X < 90% -> Very Good\n",
    "    70% < X < 80% -> Good\n",
    "    60% < X < 70% -> Poor\n",
    "    X < 60% -> Rubbish"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "#### Test model with test data"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 67,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>RowNumber</th>\n",
       "      <th>CustomerId</th>\n",
       "      <th>Surname</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Geography</th>\n",
       "      <th>Gender</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>HasCrCard</th>\n",
       "      <th>IsActiveMember</th>\n",
       "      <th>EstimatedSalary</th>\n",
       "      <th>Exited</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>10001</td>\n",
       "      <td>15798485</td>\n",
       "      <td>Copley</td>\n",
       "      <td>565</td>\n",
       "      <td>France</td>\n",
       "      <td>Male</td>\n",
       "      <td>31</td>\n",
       "      <td>1</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>20443.08</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>10002</td>\n",
       "      <td>15588959</td>\n",
       "      <td>T'ang</td>\n",
       "      <td>569</td>\n",
       "      <td>France</td>\n",
       "      <td>Male</td>\n",
       "      <td>34</td>\n",
       "      <td>4</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>4045.90</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>10003</td>\n",
       "      <td>15624896</td>\n",
       "      <td>Ku</td>\n",
       "      <td>669</td>\n",
       "      <td>France</td>\n",
       "      <td>Female</td>\n",
       "      <td>20</td>\n",
       "      <td>7</td>\n",
       "      <td>0.00</td>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>128838.67</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>10004</td>\n",
       "      <td>15639629</td>\n",
       "      <td>McConnan</td>\n",
       "      <td>694</td>\n",
       "      <td>France</td>\n",
       "      <td>Male</td>\n",
       "      <td>39</td>\n",
       "      <td>4</td>\n",
       "      <td>173255.48</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>81293.10</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>10005</td>\n",
       "      <td>15638852</td>\n",
       "      <td>Ts'ui</td>\n",
       "      <td>504</td>\n",
       "      <td>Spain</td>\n",
       "      <td>Male</td>\n",
       "      <td>28</td>\n",
       "      <td>10</td>\n",
       "      <td>109291.36</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>187593.15</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   RowNumber  CustomerId   Surname  CreditScore Geography  Gender  Age  \\\n",
       "0      10001    15798485    Copley          565    France    Male   31   \n",
       "1      10002    15588959     T'ang          569    France    Male   34   \n",
       "2      10003    15624896        Ku          669    France  Female   20   \n",
       "3      10004    15639629  McConnan          694    France    Male   39   \n",
       "4      10005    15638852     Ts'ui          504     Spain    Male   28   \n",
       "\n",
       "   Tenure    Balance  NumOfProducts  HasCrCard  IsActiveMember  \\\n",
       "0       1       0.00              1          0               1   \n",
       "1       4       0.00              1          0               1   \n",
       "2       7       0.00              2          1               0   \n",
       "3       4  173255.48              1          1               1   \n",
       "4      10  109291.36              1          1               1   \n",
       "\n",
       "   EstimatedSalary  Exited  \n",
       "0         20443.08       0  \n",
       "1          4045.90       0  \n",
       "2        128838.67       0  \n",
       "3         81293.10       0  \n",
       "4        187593.15       0  "
      ]
     },
     "execution_count": 67,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_test = pandas.DataFrame.from_csv('Churn-Modelling-Test-Data.csv', index_col=None)\n",
    "df_test[:5]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 68,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df_test = sm.add_constant(df_test)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 69,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df_test_y = df_test['Exited']\n",
    "df_test = df_test.drop(['Exited'], axis = 1)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 70,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>France</th>\n",
       "      <th>Germany</th>\n",
       "      <th>Spain</th>\n",
       "      <th>Female</th>\n",
       "      <th>Male</th>\n",
       "      <th>const</th>\n",
       "      <th>RowNumber</th>\n",
       "      <th>CustomerId</th>\n",
       "      <th>Surname</th>\n",
       "      <th>CreditScore</th>\n",
       "      <th>Geography</th>\n",
       "      <th>Gender</th>\n",
       "      <th>Age</th>\n",
       "      <th>Tenure</th>\n",
       "      <th>Balance</th>\n",
       "      <th>NumOfProducts</th>\n",
       "      <th>HasCrCard</th>\n",
       "      <th>IsActiveMember</th>\n",
       "      <th>EstimatedSalary</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>10001</td>\n",
       "      <td>15798485</td>\n",
       "      <td>Copley</td>\n",
       "      <td>565</td>\n",
       "      <td>France</td>\n",
       "      <td>Male</td>\n",
       "      <td>31</td>\n",
       "      <td>1</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>20443.08</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>10002</td>\n",
       "      <td>15588959</td>\n",
       "      <td>T'ang</td>\n",
       "      <td>569</td>\n",
       "      <td>France</td>\n",
       "      <td>Male</td>\n",
       "      <td>34</td>\n",
       "      <td>4</td>\n",
       "      <td>0.00</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>4045.90</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>10003</td>\n",
       "      <td>15624896</td>\n",
       "      <td>Ku</td>\n",
       "      <td>669</td>\n",
       "      <td>France</td>\n",
       "      <td>Female</td>\n",
       "      <td>20</td>\n",
       "      <td>7</td>\n",
       "      <td>0.00</td>\n",
       "      <td>2</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>128838.67</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>10004</td>\n",
       "      <td>15639629</td>\n",
       "      <td>McConnan</td>\n",
       "      <td>694</td>\n",
       "      <td>France</td>\n",
       "      <td>Male</td>\n",
       "      <td>39</td>\n",
       "      <td>4</td>\n",
       "      <td>173255.48</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>81293.10</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>0</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>0</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>10005</td>\n",
       "      <td>15638852</td>\n",
       "      <td>Ts'ui</td>\n",
       "      <td>504</td>\n",
       "      <td>Spain</td>\n",
       "      <td>Male</td>\n",
       "      <td>28</td>\n",
       "      <td>10</td>\n",
       "      <td>109291.36</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>187593.15</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   France  Germany  Spain  Female  Male  const  RowNumber  CustomerId  \\\n",
       "0       1        0      0       0     1      1      10001    15798485   \n",
       "1       1        0      0       0     1      1      10002    15588959   \n",
       "2       1        0      0       1     0      1      10003    15624896   \n",
       "3       1        0      0       0     1      1      10004    15639629   \n",
       "4       0        0      1       0     1      1      10005    15638852   \n",
       "\n",
       "    Surname  CreditScore Geography  Gender  Age  Tenure    Balance  \\\n",
       "0    Copley          565    France    Male   31       1       0.00   \n",
       "1     T'ang          569    France    Male   34       4       0.00   \n",
       "2        Ku          669    France  Female   20       7       0.00   \n",
       "3  McConnan          694    France    Male   39       4  173255.48   \n",
       "4     Ts'ui          504     Spain    Male   28      10  109291.36   \n",
       "\n",
       "   NumOfProducts  HasCrCard  IsActiveMember  EstimatedSalary  \n",
       "0              1          0               1         20443.08  \n",
       "1              1          0               1          4045.90  \n",
       "2              2          1               0        128838.67  \n",
       "3              1          1               1         81293.10  \n",
       "4              1          1               1        187593.15  "
      ]
     },
     "execution_count": 70,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "dummy = pandas.get_dummies(df_test['Gender'])\n",
    "df_test= dummy.join(df_test)\n",
    "dummy = pandas.get_dummies(df_test['Geography'])\n",
    "df_test = dummy.join(df_test)\n",
    "df_test[:5]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 71,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df_test['Balance_log'] = df_test['Balance'].map(lambda x: np.log10(x+1))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Check the parameters included in the final model (model11) and rerun the model with the same parameters but now including the test data:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 125,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "const            -3.912576\n",
       "Germany           0.747595\n",
       "Female            0.526721\n",
       "CreditScore      -0.000675\n",
       "Age               0.072655\n",
       "Tenure           -0.015879\n",
       "NumOfProducts    -0.095020\n",
       "IsActiveMember   -1.075776\n",
       "Balance_log       0.069026\n",
       "dtype: float64"
      ]
     },
     "execution_count": 125,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "model11.params"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 126,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df_test = df_test[['Germany', 'Female', 'CreditScore', 'Age', 'Tenure', 'NumOfProducts', 'IsActiveMember', 'Balance_log', 'const']]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 127,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df_total = df_final.append(df_test)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 128,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Optimization terminated successfully.\n",
      "         Current function value: 0.428257\n",
      "         Iterations 6\n"
     ]
    }
   ],
   "source": [
    "model_final = smdis.Logit(df_y, df_total[:10000]).fit()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 139,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "array([ 0.03811657,  0.04476137,  0.06383845,  0.08165338,  0.03915974])"
      ]
     },
     "execution_count": 139,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "y_pred = model_final.predict(df_total[10000:])\n",
    "y_pred[:5]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Let's construct the CAP curve:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 142,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>phat</th>\n",
       "      <th>Exited</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>957</th>\n",
       "      <td>0.877614</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>249</th>\n",
       "      <td>0.857731</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>429</th>\n",
       "      <td>0.832754</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>209</th>\n",
       "      <td>0.826484</td>\n",
       "      <td>1</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>693</th>\n",
       "      <td>0.826381</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "         phat  Exited\n",
       "957  0.877614       1\n",
       "249  0.857731       1\n",
       "429  0.832754       1\n",
       "209  0.826484       1\n",
       "693  0.826381       0"
      ]
     },
     "execution_count": 142,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_pred = pandas.DataFrame()\n",
    "df_pred['phat'] = y_pred\n",
    "df_pred['Exited'] = df_test_y\n",
    "df_pred = df_pred.sort( 'phat', ascending=False)\n",
    "df_pred[:5]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 152,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Total Exited: 260\n",
      "Total number of clients: 1000\n",
      "Ratio of exits: 0.26\n"
     ]
    }
   ],
   "source": [
    "Total_Exited = sum(df_pred['Exited'])\n",
    "print \"Total Exited: %s\" % Total_Exited\n",
    "Total_Records = len(df_pred)\n",
    "print \"Total number of clients: %s\" % Total_Records\n",
    "Exit_Ratio = Total_Exited/Total_Records\n",
    "print \"Ratio of exits: %s\" % Exit_Ratio"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 143,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df_pred['total_selected'] = range(1,len(df_pred)+1)\n",
    "df_pred['total_selected_per'] = df_pred['total_selected'].map(lambda x: round(x*100/Total_Records, 1))\n",
    "df_pred['random_selection'] = df_pred['total_selected'].map(lambda x: x*Exit_Ratio)\n",
    "df_pred['random_selection_per'] = df_pred['total_selected'].map(lambda x: round(x*100/Total_Records, 1))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 144,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df_pred = df_pred.sort('phat', ascending=False)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 145,
   "metadata": {
    "collapsed": true
   },
   "outputs": [],
   "source": [
    "df_pred['model_select'] = df_pred.Exited.cumsum()\n",
    "df_pred['model_select_per'] = df_pred['model_select'].map(lambda x: round(x*100/Total_Exited, 1))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 146,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>phat</th>\n",
       "      <th>Exited</th>\n",
       "      <th>total_selected</th>\n",
       "      <th>total_selected_per</th>\n",
       "      <th>random_selection</th>\n",
       "      <th>random_selection_per</th>\n",
       "      <th>model_select</th>\n",
       "      <th>model_select_per</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>957</th>\n",
       "      <td>0.877614</td>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>0.1</td>\n",
       "      <td>0.26</td>\n",
       "      <td>0.1</td>\n",
       "      <td>1</td>\n",
       "      <td>0.4</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>249</th>\n",
       "      <td>0.857731</td>\n",
       "      <td>1</td>\n",
       "      <td>2</td>\n",
       "      <td>0.2</td>\n",
       "      <td>0.52</td>\n",
       "      <td>0.2</td>\n",
       "      <td>2</td>\n",
       "      <td>0.8</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>429</th>\n",
       "      <td>0.832754</td>\n",
       "      <td>1</td>\n",
       "      <td>3</td>\n",
       "      <td>0.3</td>\n",
       "      <td>0.78</td>\n",
       "      <td>0.3</td>\n",
       "      <td>3</td>\n",
       "      <td>1.2</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>209</th>\n",
       "      <td>0.826484</td>\n",
       "      <td>1</td>\n",
       "      <td>4</td>\n",
       "      <td>0.4</td>\n",
       "      <td>1.04</td>\n",
       "      <td>0.4</td>\n",
       "      <td>4</td>\n",
       "      <td>1.5</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>693</th>\n",
       "      <td>0.826381</td>\n",
       "      <td>0</td>\n",
       "      <td>5</td>\n",
       "      <td>0.5</td>\n",
       "      <td>1.30</td>\n",
       "      <td>0.5</td>\n",
       "      <td>4</td>\n",
       "      <td>1.5</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "         phat  Exited  total_selected  total_selected_per  random_selection  \\\n",
       "957  0.877614       1               1                 0.1              0.26   \n",
       "249  0.857731       1               2                 0.2              0.52   \n",
       "429  0.832754       1               3                 0.3              0.78   \n",
       "209  0.826484       1               4                 0.4              1.04   \n",
       "693  0.826381       0               5                 0.5              1.30   \n",
       "\n",
       "     random_selection_per  model_select  model_select_per  \n",
       "957                   0.1             1               0.4  \n",
       "249                   0.2             2               0.8  \n",
       "429                   0.3             3               1.2  \n",
       "209                   0.4             4               1.5  \n",
       "693                   0.5             4               1.5  "
      ]
     },
     "execution_count": 146,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_pred[:5]"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 151,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "image/png": "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\nGoOnLN2uCcRFnL8Lp1K+6Lejv/Hd3u+wiY1nVj3DrTG3Fjg/qvUonrr5KYuis5ZWHaz33//+l9de\ne4133nmHDh06EBoaypYtW3jttdd48MEHCQoKKrNYjDFcccUVzJ4925FA5ObmsmDBAho0aFBmcXgC\n7510XUbS07X/Qfm+Bxc/yNr9a9l1bBf/+8f/+KLHFwUe/pg86M6ZnuH06dOMHTuWd999l65duxIa\nah8ea9q0KXPmzHEkD6dPn6ZPnz7UrFmT2NhYXnrpJcc1RIRx48YRExND7dq16devH6dPn3acnzNn\nDjExMdSoUYPx48dfNKY777yT77//nlOn7Pu1fP311zRt2pTatWu75J4iwoQJE2jQoAE1atSgR48e\njs2+PIkmEBeha0AoXzd/+3y2H9nOh10/5H+d/kfvpr2tDslyK1dCkyaQmWmvOuiQhXXWr19PVlYW\nnTsXvx/KI488wpkzZ9i3bx/x8fHMnj2bGTNmADBjxgxmz57N6tWr2bt3L2fOnOGRRx4BYMeOHQwb\nNoy5c+eSlJTEsWPHSExMLPZeISEhdOnShY8//hiA2bNn06dPH/JvSFaae7711lssXryYtWvXkpSU\nRNWqVRk2bFjJPzw30904L+Lnn5uSmroVgObN11OlSqsyua9S7mATGwMWDSjQ47D50GbeveNd7rtW\nd5j19V6H0uzGaZ4v/bi7jC357+25c+cyevRokpKSHMfatGnDjh07yMzMZPny5dx4442EhISwdetW\nrrjC/g+9Dz74gI8//piVK1fSvn17unXrxpAhQwDYtWsXjRs3Jj09nZdeeomdO3c6tsJOS0ujatWq\nLFu2rNCGxv79+1OvXj1uv/12Ro8ezbJly7jyyivZt28f7du3Z9CgQfTp06dU97z66quZPHkyt95q\nH0o8ePAg0dHRZGRkcODAAerXr092dnaZNVHqbpyXQMRGevpux3NdREp5g5SsFPae2FvouZ8Sf2JL\n8hb+2+G/jmOVy1fm+ojryyo8j6W9DsW7lD/+rlC9enWOHj2KzWZz/MH84YcfAIiKisJms3H06FFy\ncnKIijo3Sy46Otrxr/qkpCSio6MLnMvJySE5OZmkpCTq1avnOFexYkWqV69+0bjatGnDkSNHeOml\nl7jzzjspX758gfOluWdCQgJdu3Z1vF8RISgoiOTk5It/YGVIE4hiZGYewGZLByAoKJygoGoWR6RU\n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\n5pprJC0tTTp06CBPPPFEka+988475bLLLpOqVate8L933XVXkd8XExMjtWvXlpo1a8rtt98uW7Zs\ncZzr0qWLTJw4scDrK1WqJJs2bZJjx45J48aN5eTJk/L2229L9+7d5cCBA9KyZUvJzs4u0fvMzMyU\n6tWry2+//eY41rx5c/n8888Lff2wYcMueI9nv27atGmx9zpx4oTUqVNHwsPDZdasWcW+tsifm8IO\nevrD3T/sW7fe5UggkpM/duu9lHdYt3+d1Hu9npzOOH3xF3v4L2NvsmKFSEyMSP/+IidOWB2N9ytV\nAlHaxLuUiXWXLl2kcePG0rRpU8nKyirVtQqzbt06ycjIkPT0dHn55Zeldu3acurUKRERue222+T9\n998v8Pq6devK6tWrRUTk448/luuuu046deok+/fvl3vuuUdWrlwpn3zyidxyyy1y9913S2JiolNx\nDB06VP7zn/+IiMivv/4q1apVc8v7FRFp3769hIaGyunTxf9e0wSiBDZsaORIIE6f3uTWeynP8tep\nv+Snv34q8Pho20cS+lKovP3j285dRBOIUtOqg3t4awVCROTLL7+UgIAAmTp1apnc78orr5QlS5aI\niD15efXVVwucDwsLk02bLvz7sGTJEunVq5ccP35c6tWrJykpKfLhhx9Kjx49nLrv+vXrpX79+iIi\n8uSTT8qQIUNK+U4KN2fOHGnYsKF06dLlovco6udGeyDOY7Nlk5Gx1/FcV6H0H1sObeHvc/5O9GXR\nF5ybctcUejbuaUFU/kd7HdT5UlNTGTFiBAMHDuS5557jn//8J5cV8YPRqVMn1q5dW2jT8s0338zS\npUudumde4yAA11xzDVu2bHGc++OPP8jOzqZRo4J/HzIyMhgzZgxff/01u3fvJioqitDQUFq2bMnL\nL7/s1H1btWpFcHAwa9euZd68eQVmS5xv6NChfPjhhxe8VxEhJiaGbdu2Ffp9hw8fZuTIkSxcuJBG\njRpx7bXX8sADDzh6PJxWWFbh6Q/cmC2npv7uqD6sWxfptvsoz/HVrq/krQ1vSZWXq8jz8c+X/oJe\n8K85T6RVB/fDSysQAwYMkJ49e4qIyEMPPSTdu3d36fX3798vP/zwg2RlZUlGRoZMnDhRatasKceP\nHxcRke3bt0uVKlXk+++/l5SUFOnVq5f06tXrguuMGTNG3njjDREROXjwoISHh0tycrK8++67jv6L\n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\n",
      "text/plain": [
       "<matplotlib.figure.Figure at 0x1086bc0d0>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "plt.plot(df_pred['total_selected_per'], df_pred['random_selection_per'], label = 'random model' )\n",
    "plt.plot(df_pred['total_selected_per'], df_pred['model_select_per'], label = 'Good Model' )\n",
    "plt.plot([50, 50], [76, 0], 'r--', lw=1, label = 'x = 50%, y = X')\n",
    "plt.plot([0, 50], [76, 76], 'r--', lw=1)\n",
    "plt.plot([10, 100], [99, 99], 'y-', lw=3, label = 'perfect model')\n",
    "plt.plot([0, 10], [0, 99], 'y-', lw=3)\n",
    "plt.legend(bbox_to_anchor=(1, 1), loc='upper left', ncol=1)\n",
    "plt.xlabel('Total Contacted')\n",
    "plt.ylabel('Total Purchased')\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "__Conclusions__:\n",
    "\n",
    "- The model is performing above 80%, so a bit worse than the training data (a drop of about 3%).\n",
    "- The concentration of data is about 10 times less than the training data which explains the ruggedness of the curve."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Parameters Interpretation"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Odds ratio:\n",
    "\n",
    "$$odds = \\frac{p}{1-p}$$\n",
    "\n",
    "Multiple logistic regression:\n",
    "\n",
    "$$ln(\\frac{p}{1-p}) = b_0 + b_0x_1 + ... + b_nx_n$$\n",
    "\n",
    "So we can write:\n",
    "\n",
    "$$ln(odds) = b_0 + b_0x_1 + ... + b_nx_n$$\n",
    "\n",
    "$$<=> odds = e^{b_0 + b_0x_1 + ... + b_nx_n}$$\n",
    "\n",
    "$$<=> odds = e^{b_0} + e^{b_0x_1} + ... + e^{b_nx_n}$$\n",
    "\n",
    "which means that, if we increase an unit to the $$x_1$$ variable,  for example:\n",
    "\n",
    "$$e^{b_1x_1} -> e^{b_1(x_1 + 1)} = e^{b_1x_1}e^{b_1}$$\n",
    "\n",
    "which means that, increading an independente variable, e.g. $$x_1$$, by 1 unit, will increase the odds by a multiplicative factor of $$e^{b_i}$$\n",
    "\n",
    "So let's transform the coefficients of our model into odds ratios:\n",
    "\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 166,
   "metadata": {
    "collapsed": false
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "Age               1.075360\n",
       "Balance_log       1.071464\n",
       "CreditScore       0.999325\n",
       "Female            1.693371\n",
       "Germany           2.111916\n",
       "IsActiveMember    0.341033\n",
       "NumOfProducts     0.909355\n",
       "Tenure            0.984246\n",
       "const             0.019989\n",
       "dtype: float64"
      ]
     },
     "execution_count": 166,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "odds_ratio = np.exp(model_final.params)\n",
    "odds_ratio"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "- \"Germany\" has the greater odds ratio, which means that being in Germany has the highest impact for people leaving the bank - most important independent variable that influences the model. If we change from not Germany to Germany, the odds ratio increases by 2.1.\n",
    "- The next one is gender. One we go from a male customer to a female customer with all else held constant, the odds ratio of a female custormer leaving is 1.7 times grater that a Male customer leaving. So basically the odds ratio increases by 70%.\n",
    "- Being an \"ActiveMember\" has the oppositive effect because the value is less than 1. This means that when we go from a member who is not active to a member that is active, the odds ratio gets multiplied by 0.34. So basically it drops by 66% which is also great in terms of retention.\n",
    "- Anything above 0.8 and bellow 1.2 has not a great impact and can be ignored.\n"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 2",
   "language": "python",
   "name": "python2"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 2
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython2",
   "version": "2.7.10"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 0
}




