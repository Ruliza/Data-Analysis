##########################################
#
# Name: Luiza Novytska
# Project Name: Comparison of death cases reported in different states of the USA for
# influenza, pneumonia, and COVID-19, between 2020 and 2023 for different age groups. 
#
##########################################


import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import random
import statistics
import time  # this is my +1 extra to the course material :)




'''
Docstring:
Calculates the arithmetic mean of a list or pandas Series.
Returns a float: The arithmetic mean of the input data.
'''
def mean(data):
    total = 0
    if isinstance(data, (pd.Series)):
        total = data.sum()
    else:
        for val in data:
            total += val
    return total / len(data)


'''
Finds the index of the median element in a sorted list or pandas Series.
Returns a tuple: A tuple containing the indices of the two elements surrounding the median value.
If the length of the input is odd, the first element of the tuple will be the index of the median value.
'''
def median_elem(data):
    new_data = data.copy()
    
    if not len(new_data) % 2:
        med1 = int(len(new_data) / 2)
        med2= med1 - 1
        return med1, med2
    else:
        med = int((len(new_data) -1) / 2)
        return med, med


'''
Calculates the median of a list or pandas Series.
Returns a float: The median of the input data.
'''
def median(data):
    new_list = data.copy()
    new_list = new_list.sort_values()
    length = len(new_list)
    if length % 2 == 0:
        med1, med2 = length // 2, length // 2 - 1
        return (new_list.iloc[med1] + new_list.iloc[med2]) / 2
    else:
        med1 = length // 2
        return new_list.iloc[med1]
    
    
'''
Calculates the mode(s) of a list or pandas Series.
Returns a list: A list of one or more elements that occur most frequently in the input data.
'''
def mode(data):
    uniques = dict()
    
    for item in data:
        if item not in uniques:
            uniques[item] = 1
        else:
            uniques[item] += 1
    
    modes = []
    
    for key, value in uniques.items():
        if value == max(uniques.values()):
            modes.append(key)
    
    return modes

'''
Calculates the data range of a list or pandas Series.
Returns a float: The difference between the maximum and minimum values in the input data.
'''
def data_range(data):
    new_list = data.copy()
    if isinstance(new_list, pd.Series):
        new_list = new_list.dropna()
        if len(new_list) == 0:
            return np.nan
        else:
            low = new_list.min()
            high = new_list.max()
            return high-low
    else:
        new_list.sort()
        low = new_list[0]
        high = new_list[-1]
        if pd.isna(low) or pd.isna(high):
            return np.nan
        else:
            return high-low
    
'''
Calculates the interquartile range (IQR) of a list or pandas Series.
Returns a float: The difference between the third quartile (Q3) and the first quartile (Q1) of the input data.

'''
def iqr(data):
    new_series = data.copy()
    new_series.sort_values(inplace=True)

    index1, index2 = median_elem(new_series)
    lower = new_series[:index2]
    higher = new_series[index2:]

    lindex1, lindex2 = median_elem(lower)
    hindex1, hindex2 = median_elem(higher)

    return higher.iloc[hindex1] - lower.iloc[lindex1]


'''
Calculates the variance of a list or pandas Series.
Returns a float: The variance of the input data, which is the average of the squared differences between each value and the mean.
'''
def variance(data, group=2):
    avg = mean(data)
    
    summation = 0
    for num in data:
        summation += (num - avg) ** 2
        
    if group == 2:
        return summation / (len(data) -1)
    else:
        return summation / len(data)

'''
Calculates the standard deviation of a list or pandas Series.
Returns a float: The standard deviation of the input data, which is the square root of the variance.
'''
def std_dev(data, group=2):
    return variance(data, group) ** .5
    




'''
The main function of the analysis.
The outcome includes : opening the file for reading, composing data sets by filtering the columns and rows that are only to be used in analysis.
Plotting 3 independant graphs, one comperison graph and a bar chart.
Error handling is present along the coding accordingly.
'''
def main():
    
    
##############--- Opening the file ---###############################################################################

    start = time.time()

    try:
        with open ('Provisional_Death_Counts_for_Influenza__Pneumonia__and_COVID-19.csv', 'r') as my_file:
            data = pd.read_csv(my_file)
            
    except:
        print("File not found")
    else:
        print(f"Data loaded, this operation took {time.time() - start} seconds.")

    print(data.columns)
    print(data.head)


######--- Building up the 3 data sets for further chart plotting ---###############################################


    '''
    Pandas package is using Series to iterate through each element in the column using boolean comperison. It`s much faster than the for loop.
    Creating a Covid-19 deaths table with filters on US location and all ages patients.
    '''
    US =  data.loc[data.Jurisdiction == 'United States']

    New = US.loc[US.AgeGroup == 'All Ages']        

    print(f'Here is the table filteres to see the US for all age groups :\n {New}')
       # this is the main data filtered set we are going to work with in the first 3 grafths 


#################--- Plotting 4 graths using the data sets we built ---############################################


    '''
    These charts compare deaths from 3 causes for the whole country and all ages, range 2020-2023
    '''
    plt.style.use('Solarize_Light2')

    fig1=plt.figure(1)
    plt.title('Covid-19 deaths numbers 2020-23')
    plt.plot(New['Week Ending Date'], New['COVIDDeaths'], label='Covid-19')
    plt.xticks(New['Week Ending Date'][::18])
    plt.xlabel('Date')
    plt.ylabel('Mortality Number')
    plt.legend()
    fig1.savefig("1.png")
    plt.show()

    fig2=plt.figure(2)
    plt.title('Influenza deaths numbers 2020-23')
    plt.plot(New['Week Ending Date'], New['InfluenzaDeaths'], label='Influenza')
    plt.xticks(New['Week Ending Date'][::18])
    plt.xlabel('Date')
    plt.ylabel('Mortality Number')
    plt.legend()
    fig2.savefig("2.png")
    plt.show()

    fig3=plt.figure(3)
    plt.title('Pneumonia deaths numbers 2020-23')
    plt.plot(New['Week Ending Date'], New['PneumoniaDeaths'], label='Pneumonia')
    plt.xticks(New['Week Ending Date'][::18])
    plt.xlabel('Date')
    plt.ylabel('Mortality Number')
    plt.legend()
    fig3.savefig("3.png")
    plt.show()


    fig4=plt.figure(4)
    plt.title('Comparison Covid vs Influensa vs Pneumonia 2020-23')
    plt.plot(New['Week Ending Date'], New['COVIDDeaths'], label='Covid-19')
    plt.plot(New['Week Ending Date'], New['InfluenzaDeaths'], label='Influenza')
    plt.plot(New['Week Ending Date'], New['PneumoniaDeaths'], label='Pneumonia')
    plt.xticks(New['Week Ending Date'][::18])
    plt.xlabel('Year')
    plt.ylabel('Mortality Number')
    plt.legend()
    fig4.savefig("4.png")
    plt.show()





#######--- Filtering the data for the states only ---#######################################################


    States =  data.loc[data.Jurisdiction != 'United States']
    StatesOne = States.loc[States.Jurisdiction != 'HHS Region 1']
    StatesTwo = StatesOne.loc[StatesOne.Jurisdiction != 'HHS Region 2']
    StatesThree = StatesTwo.loc[StatesTwo.Jurisdiction != 'HHS Region 3']
    StatesFour = StatesThree.loc[StatesThree.Jurisdiction != 'HHS Region 4']
    StatesFive = StatesFour.loc[StatesFour.Jurisdiction != 'HHS Region 5']
    StatesSix = StatesFive.loc[StatesFive.Jurisdiction != 'HHS Region 6']
    StatesSeven = StatesSix.loc[StatesSix.Jurisdiction != 'HHS Region 7']
    StatesEight = StatesSeven.loc[StatesSeven.Jurisdiction != 'HHS Region 8']
    StatesNine = StatesEight.loc[StatesEight.Jurisdiction != 'HHS Region 9']
    StatesTen = StatesNine.loc[StatesNine.Jurisdiction != 'HHS Region 10']


    Ages = StatesTen.loc[StatesTen.AgeGroup == 'All Ages']

    print(f'Here is the table filteres to see each state for all age groups :\n {Ages}')


#################--- plotting a bar chart ---################################################################


    fig5=plt.figure(5)
    plt.title('All states COVID-19 vs Pneumonia mortality for All age groups for 2020-2023 in total')
    plt.bar(Ages['Jurisdiction'], Ages['COVIDDeaths'], label='Covid-19')
    plt.bar(Ages['Jurisdiction'], Ages['PneumoniaDeaths'], label='Pneumonia')
    plt.xticks(Ages['Jurisdiction'], rotation='vertical', size=8)
    plt.xlabel('State Name')
    plt.ylabel('Mortality Number')
    plt.legend()
    fig5.savefig("5.png")
    plt.show()
      

#############################################################################################################
# using the STATISTICS lib to print out the mean, median, variance and numpy lib for iqr
# versus the hand written functions above  
    
    
    while True:
        try:
            pop_or_samp = int(input("Choose [1] for for population or [2] for sample: "))
            break
        
        except:
            raise ValueError("Only integers are allowed")


    print(f"stat mean Covid-19, US, all age groups:         {statistics.mean(New['COVIDDeaths'])}")
    print(f"mean by hand:                                   {mean(New['COVIDDeaths'])}\n")
    print(f"stat mean Pneumonia, US, all age groups:        {statistics.mean(New['PneumoniaDeaths'])}")
    print(f"mean by hand:                                   {mean(New['PneumoniaDeaths'])}\n")    



    print(f"stat median Covid-19, US, all age groups:        {int(statistics.median(New['COVIDDeaths']))}")
    print(f"median by hand:                                  {median(New['COVIDDeaths'])}\n")
    print(f"stat median Pneumonia, US, all age groups:       {int(statistics.median(New['PneumoniaDeaths']))}")
    print(f"median by hand:                                  {median(New['PneumoniaDeaths'])}\n")
       


    print(f"stat mode Covid-19, US, all age groups:          {statistics.mode(New['COVIDDeaths'])}")
    print(f"mode by hand Covid-19, US, all age groups:       {mode(New['COVIDDeaths'])}\n")
    print(f"stat mode Pneumonia, US, all age groups:         {statistics.mode(New['PneumoniaDeaths'])}")
    print(f"mode by hand Pneumonia, US, all age groups:      {mode(New['PneumoniaDeaths'])}\n")
    
    

    print(f"Range by hand Covid-19, US, all age groups:      {data_range(New['COVIDDeaths'])}\n")
    print(f"Range by hand Pneumonia, US, all age groups:     {data_range(New['PneumoniaDeaths'])}\n")
    
    

    print(f"iqr numpy Covid-19, US, all age groups:          {np.percentile(New['COVIDDeaths'],75,method='midpoint') - np.percentile(New['COVIDDeaths'],25,method='midpoint')}")
    print(f"iqr by hand Covid-19, US, all age groups:        {iqr(New['COVIDDeaths'])}\n")
    print(f"iqr numpy Pneumonia, US, all age groups:         {np.percentile(New['PneumoniaDeaths'],75,method='midpoint') - np.percentile(New['PneumoniaDeaths'],25,method='midpoint')}")
    print(f"iqr by hand Pneumonia, US, all age groups:       {iqr(New['PneumoniaDeaths'])}\n")



    print(f"stat pop variance Covid-19, US, all age groups:  {statistics.pvariance(New['COVIDDeaths'])}")
    print(f"stat sample var Covid-19, US, all age groups:    {statistics.variance(New['COVIDDeaths'])}")
    print(f"variance by hand Covid-19, US, all age groups:   {variance(New['COVIDDeaths'], pop_or_samp)}\n")
    print(f"stat pop variance Pneumonia, US, all age groups: {statistics.pvariance(New['PneumoniaDeaths'])}")
    print(f"stat sample var Pneumonia, US, all age groups:   {statistics.variance(New['PneumoniaDeaths'])}")
    print(f"variance by hand Pneumonia, US, all age groups:  {variance(New['PneumoniaDeaths'], pop_or_samp)}\n")


    print(f"stat pop SD Covid-19, US, all age groups:        {statistics.pstdev(New['COVIDDeaths'])}")
    print(f"stat sample SD Covid-19, US, all age groups:     {statistics.stdev(New['COVIDDeaths'])}")
    print(f"SD by hand Covid-19, US, all age groups:         {std_dev(New['COVIDDeaths'], pop_or_samp)}\n")
    print(f"stat pop SD Pneumonia, US, all age groups:       {statistics.pstdev(New['PneumoniaDeaths'])}")
    print(f"stat sample SD Pneumonia, US, all age groups:    {statistics.stdev(New['PneumoniaDeaths'])}")
    print(f"SD by hand Pneumonia, US, all age groups:        {std_dev(New['PneumoniaDeaths'], pop_or_samp)}\n")
    


     


if __name__ == "__main__":
    
    print("So many people came to see my boring presentation... Thank you!")
    print(input("Are you ready to start?"))
    
    main()
    

    print("The end of the analysis.\nThank you for your attention!")   

