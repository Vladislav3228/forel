# forel

#include <iostream>
#include <vector>
#include <cmath>
#include <map>
using namespace std;

double dist(vector<double> a, vector<double> b){
    double d = 0;
    for (int i = 0; i < a.size(); i++){
        d+=(a[i]-b[i])*(a[i]-b[i]);
    }
    return sqrt(d);
}

vector<double> init_center(vector< vector<double> > data){
    return data[rand()%data.size()];
}

vector< vector <double> > clustering(vector< vector<double> > data, vector<double> center, double width){
    int i;
    double range;
    vector< vector <double> > cluster;
    for (i = 0; i < data.size(); i++){
        range = dist(center, data[i]);
        if(range <= width){
            cluster.push_back(data[i]);
        }
    }
    //cout<<"clustering"<<endl;
    return cluster;
}

vector<double> avg(vector< vector <double> > cluster){
    int i, j;
    vector<double> new_center(cluster[0].size());
    for (i = 0; i < cluster.size(); i++){
        for (j = 0; j < cluster[0].size(); j++){
            new_center[j]+=cluster[i][j];
        }
    }
    for (j = 0; j < cluster[0].size(); j++){
        new_center[j]/=cluster.size();
    }
    //cout<<"avg"<<endl;
    return new_center;
}

void delete_points(vector< vector< double > > &data, vector< vector< double > > cluster){
    for(int i = 0; i < cluster.size(); i++){
        for(int j = 0; j < data.size(); j++){
            if(cluster[i] == data[j]){
                data.erase(data.begin() + j);
            }
        }
    }
}

void print_v1(vector<double> v){
    int i, j;
    for(i = 0 ; i < v.size(); i++){
        cout << v[i]<< ' ';
    }
    cout<<endl;
}

void print_v2(vector< vector<double> > data){
    int i, j;
    for(i = 0 ; i < data.size(); i++){
        for(j = 0 ; j < data[0].size() ; j++){
            cout << data[i][j] << ' ';
        }
        cout << endl;
    }
}

void print_m(map< int , vector< vector< double > > > m){
    for(auto it = m.begin(); it != m.end(); it++)
    {
        cout<<it->first<<endl;
        print_v2(it->second);
    }
}

vector< vector<double> > init_data(int n, int m){
    vector< vector<double> > data(n, vector<double>(m));
    for(int i = 0 ; i < n; i++){
        for(int j = 0 ; j < m ; j++){
            data[i][j] = rand()%10;
        }
    }
    return data;
}

int main()
{
    int i, n, m;
    double width = 5;
    map< int , vector< vector<double> > > result;
    cin>>n>>m;
    vector< vector< double > > data = init_data(n, m);
    for(i = 0 ; !data.empty() ; i++){
        vector< double > old_center = init_center(data);
        vector< vector< double > > cluster = clustering(data, old_center, width);
        vector< double > new_center = avg(cluster);
        while (new_center != old_center){
            old_center = new_center; 
            cluster = clustering(data, old_center, width);
            new_center = avg(cluster);
        }
        result[i] = cluster;
        delete_points(data, cluster);
    }
    print_m(result);
    cout<<"help me";
    return 0;
}
