---
layout: post
title: "Genetic Algorithm Mastermind Game"
categories: blog
excerpt: "Genetic Algorithm Mastermind Game"
date: 2016-02-10T20:55:37+08:00
search_omit: true_
share: true
---

For those who already learned about genetic algorithm is an AI method for finding the optimal number mimicking genetical mutation, and cross-over or mating in order to find the finest generation.
Following, I include declaration of struct for receiving values
{% highlight c++ %}
struct GAMMVALUE {
	std::string strValue;
	unsigned int iFitness;
};
{% endhighlight %}
Inside of object, the above ``GAMMVALUE`` is declared as ```m_vParent``` and ```m_vChild```.
value of the chromosomes is declared in ``std::string`` type as ``strValue``, and record the fitness point or number of errors: ``iFitness``. For proliferation of population, randomize the value for the initialization:
{% highlight c++ %}
for (int j=0; j<iSize; ++j)
	gaVal.strValue += (rand() % 57) + 48;
{% endhighlight %}
Later push the value to ```m_vParent```.
{% highlight c++ %}
m_vParent.push_back(gaVal);
{% endhighlight %}
Method of mutation is shown as below:
{% highlight c++ %}
void CGaMasterMind::mutation(GAMMVALUE& gaVal) {
	int iSize = m_strTarget.size();
	int iPos = rand() % iSize;
	unsigned int iDelta = (rand() % 57) + 48;
	
	gaVal.strValue[iPos] = ((gaVal.strValue[iPos] + iDelta) % 39);
}
{% endhighlight %}
Method of cross-over the generation is shown as below:
{% highlight c++ %}
void CGaMasterMind::crossover() {
	int iSize1 = POPSIZE * ELITRATE;
	int iSize2 = m_strTarget.size(), iPos, iRand1, iRand2;
	
	elitism(iSize1);
	
	for (int i=iSize1; i<POPSIZE; ++i) {
		iRand1 = rand() % (POPSIZE/2);
		iRand2 = rand() % (POPSIZE/2);
		iPos   = rand() % iSize2;
		
		m_vChild[i].strValue = m_vParent[iRand1].strValue.substr(0,iPos) + 
			m_vParent[iRand2].strValue.substr(iPos,iSize1 - iPos);
		
		if (rand() < (RAND_MAX * MUTRATE)) mutation(m_vChild[i]);
	}
}
{% endhighlight %}
After passing both processes of mutation and cross-over, finding the fitness resultis produced using this method:
{% highlight c++ %}
void CGaMasterMind::operator()() {
	std::string strTarget = m_strTarget;
	int iSize = strTarget.size();
	
	for (int i=0; i<POPSIZE; ++i) {
		unsigned int iFitness = 0;
		for (int j=0; j<iSize; ++j)
			iFitness += abs(int(m_vParent[i].strValue[j] - strTarget[j]));
		m_vParent[i].iFitness = iFitness;
	}
}
{% endhighlight %}
This process will take several iterations until reaching the lowest error.